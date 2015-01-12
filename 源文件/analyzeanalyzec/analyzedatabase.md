# analyzeDatabase函数
3.1函数头

函数原型为：

static void analyzeDatabase(Parse *pParse/*参数1为解析上写文*/
, int iDb/*参数2指定分析的数据库*/
)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该接口负责对给定的数据库进行全部的分析。在sqlite3Analyze中调用。

3.2函数流程

过程如下图所示：

<img src='analyzeDatabase 调用过程.jpg'>
其中分析数据库要反复执行图1.4.1中第三步，去分析数据库中的每一个表。

主要代码如下：

sqlite3BeginWriteOperation(pParse, 0, iDb)

  iStatCur = pParse->nTab;

  pParse->nTab += 3;

  openStatTable(pParse, iDb, iStatCur, 0, 0);

  iMem = pParse->nMem+1;

assert( sqlite3SchemaMutexHeld(db, iDb, 0) );

  for(k=sqliteHashFirst(&pSchema->tblHash); k;

  k=sqliteHashNext(k)){

    Table *pTab = (Table*)sqliteHashData(k);

    analyzeOneTable(pParse, pTab, 0, iStatCur, iMem);
  }
  loadAnalysis(pParse, iDb);
}

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对一个数据库进行分析要先通过openStatTable打开系统的sqlite_stat表，然后对数据库中的每一个表进行分析，通过analyzeOneTable来完成。最后调用loadAnalysis。用到的几个函数后面会介绍。
