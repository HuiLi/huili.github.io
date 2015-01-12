# callback.c模块源码分析
<br></br>
主要功能：<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用于访问用户定义的函数和排序队列的内部哈希表功能。<br></br>
具体应用：<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3_exec()包含一个回叫(callback)机制，提供了一种从SELECT语句得到结果的方法。如果提供了回叫函数，SQLite则会在执行SELECT语句期间在遇到记录时调用回叫函数。<br></br>
重要代码:<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;调用“排序请求”回调请求一个排序序列:<br></br>

    static void callCollNeeded(sqlite3 *db, int enc, const char *zName){
      assert( !db->xCollNeeded || !db->xCollNeeded16 );
      if( db->xCollNeeded ){
        char *zExternal = sqlite3DbStrDup(db, zName);
        if( !zExternal ) return;
        db->xCollNeeded(db->pCollNeededArg, db, enc, zExternal);
        sqlite3DbFree(db, zExternal);
      }
    #ifndef SQLITE_OMIT_UTF16
      if( db->xCollNeeded16 ){
        char const *zExternal;
        sqlite3_value *pTmp = sqlite3ValueNew(db);
        sqlite3ValueSetStr(pTmp, -1, zName, SQLITE_UTF8, SQLITE_STATIC);
        zExternal = sqlite3ValueText(pTmp, SQLITE_UTF16NATIVE);
        if( zExternal ){
          db->xCollNeeded16(db->pCollNeededArg, db, (int)ENC(db), zExternal);
        }
        sqlite3ValueFree(pTmp);
      }
    #endif
    }


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;排序系统如果未能提供一个很好的排序函数，也有可能提供其他版本的排序函数的供这个程序被调用。如果它们存在使用其中的一个来代替。如果可能的话，尽量避免UTF-8<- > UTF-16转换。<br></br>

    static int synthCollSeq(sqlite3 *db, CollSeq *pColl){
      CollSeq *pColl2;
      char *z = pColl->zName;
      int i;
      static const u8 aEnc[] = { SQLITE_UTF16BE, SQLITE_UTF16LE, SQLITE_UTF8 };
      for(i=0; i<3; i++){
        pColl2 = sqlite3FindCollSeq(db, aEnc[i], z, 0);
        if( pColl2->xCmp!=0 ){
          memcpy(pColl, pColl2, sizeof(CollSeq));
          pColl->xDel = 0;         /* Do not copy the destructor 不要复制析构函数*/
          return SQLITE_OK;
        }
      }
      return SQLITE_ERROR;
    }



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;通过给定名称的功能，搜索FuncDefHash。如果找到，返回指针到FuncDef，如果没找到返回0。<br></br>

    static FuncDef *functionSearch(
      FuncDefHash *pHash,  /* Hash table to search 哈希表搜索*/
      int h,               /* Hash of the name 名称的哈西表*/
      const char *zFunc,   /* Name of function   功能的名字*/
      int nFunc            /* Number of bytes in zFunc  zFunc字节数*/
    ){
      FuncDef *p;
      for(p=pHash->a[h]; p; p=p->pHash){
        if( sqlite3StrNICmp(p->zName, zFunc, nFunc)==0 && p->zName[nFunc]==0 ){
          return p;
        }
      }
      return 0;
    }
<br></br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;插入一个新的FuncDef到哈希表FuncDefHash：

    void sqlite3FuncDefInsert(
      FuncDefHash *pHash,  /* The hash table into which to insert 哈希表插入*/
      FuncDef *pDef        /* The function definition to insert 该函数定义插入*/
    ){
      FuncDef *pOther;
      int nName = sqlite3Strlen30(pDef->zName);
      u8 c1 = (u8)pDef->zName[0];
      int h = (sqlite3UpperToLower[c1] + nName) % ArraySize(pHash->a);
      pOther = functionSearch(pHash, h, pDef->zName, nName);
      if( pOther ){
        assert( pOther!=pDef && pOther->pNext!=pDef );
        pDef->pNext = pOther->pNext;
        pOther->pNext = pDef;
      }else{
        pDef->pNext = 0;
        pDef->pHash = pHash->a[h];
        pHash->a[h] = pDef;
      }
    }

<br></br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
释放结构下所有的资源：<br></br>

    void sqlite3SchemaClear(void *p){
      Hash temp1;
      Hash temp2;
      HashElem *pElem;
      Schema *pSchema = (Schema *)p;

      temp1 = pSchema->tblHash;
      temp2 = pSchema->trigHash;
      sqlite3HashInit(&pSchema->trigHash);
      sqlite3HashClear(&pSchema->idxHash);
      for(pElem=sqliteHashFirst(&temp2); pElem; pElem=sqliteHashNext(pElem)){
        sqlite3DeleteTrigger(0, (Trigger*)sqliteHashData(pElem));
      }
      sqlite3HashClear(&temp2);
      sqlite3HashInit(&pSchema->tblHash);
      for(pElem=sqliteHashFirst(&temp1); pElem; pElem=sqliteHashNext(pElem)){
        Table *pTab = sqliteHashData(pElem);
        sqlite3DeleteTable(0, pTab);
      }
      sqlite3HashClear(&temp1);
      sqlite3HashClear(&pSchema->fkeyHash);
      pSchema->pSeqTab = 0;
      if( pSchema->flags & DB_SchemaLoaded ){
        pSchema->iGeneration++;
        pSchema->flags &= ~DB_SchemaLoaded;
      }
    }
<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;三个排序序列被复制之后，立即存储。这个字符串的指针被存储在各个排序序列：<br></br>

    static CollSeq *findCollSeqEntry(
      sqlite3 *db,          /* Database connection 数据库连接*/
      const char *zName,    /* Name of the collating sequence 整理顺序名称*/
      int create            /* Create a new entry if true 建立一个入口*/
    ){
      CollSeq *pColl;
      int nName = sqlite3Strlen30(zName);
      pColl = sqlite3HashFind(&db->aCollSeq, zName, nName);

      if( 0==pColl && create ){
        pColl = sqlite3DbMallocZero(db, 3*sizeof(*pColl) + nName + 1 );
        if( pColl ){
          CollSeq *pDel = 0;
          pColl[0].zName = (char*)&pColl[3];
          pColl[0].enc = SQLITE_UTF8;
          pColl[1].zName = (char*)&pColl[3];
          pColl[1].enc = SQLITE_UTF16LE;
          pColl[2].zName = (char*)&pColl[3];
          pColl[2].enc = SQLITE_UTF16BE;
          memcpy(pColl[0].zName, zName, nName);
          pColl[0].zName[nName] = 0;
          pDel = sqlite3HashInsert(&db->aCollSeq, pColl[0].zName, nName, pColl);

