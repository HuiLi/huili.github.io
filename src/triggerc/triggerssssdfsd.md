# Trigger的sqlite3BeginTrigger（）和sqlite3FinishTrigger（）
sqlite3BeginTrigger（）当语法分析器遇到了CREATE TRIGGER 语句的BEGIN，在触发器有动作之前，被调用。

sqlite3FinishTrigger（）在触发器被全部语法分析器的操作完成之后被调用，去完成触发器的处理过程。

主流程图：

![](5.png)

代码分析：

    void sqlite3BeginTrigger(……){
    ……

如果TEMP被指定,那么需要判断触发器名称是不是不合格。

    if (isTemp){
    if (pName2->n>0){
    	sqlite3ErrorMsg(pParse, "temporary trigger may not have qualified name");

清除触发器

    	goto trigger_cleanup;
    }
    iDb = 1;
    pName = pName1;
    }
    else{
    	iDb = sqlite3TwoPartName(pParse, pName1, pName2, &pName);
    	if (iDb < 0){
    		goto trigger_cleanup;
    	}
    }

内存错误？

    if (!pTableName || db->mallocFailed){
    	goto trigger_cleanup;
    }
    if (db->init.busy && iDb != 1){
    sqlite3DbFree(db, pTableName->a[0].zDatabase);
    pTableName->a[0].zDatabase = 0;
    }
    ……
    pTab = sqlite3SrcListLookup(pParse, pTableName);

设置iDb 的值为 1来创建临时数据库中触发器

    if (db->init.busy == 0 && pName2->n == 0 && pTab&& pTab->pSchema ==     db->aDb[1].pSchema){
    	iDb = 1;
    }
    if (db->mallocFailed) goto trigger_cleanup;
    assert(pTableName->nSrc == 1);

确保表的表名匹配数据库名称并且表存在

    if (sqlite3FixInit(&sFix, pParse, iDb, "trigger", pName) &&sqlite3FixSrcList(&sFix, pTableName)){
    	goto trigger_cleanup;
    }
    pTab = sqlite3SrcListLookup(pParse, pTableName);

表不存在

    if (!pTab){
    if (db->init.iDb == 1){

通常,当一个表被删除时,所有相关的触发器也终止了。但如果创建一个了临时的non-TEMP表触发器并且表被一个不同的数据库连接终止了，触发器不能发现数据库连接是不是被终止，所以触发器不能被删除。
Ticket #3810


    db->init.orphanTrigger = 1;
    }
    goto trigger_cleanup;
    }

不能虚表上创建触发器

    if (IsVirtual(pTab)){
        sqlite3ErrorMsg(pParse, "cannot create triggers on virtual tables");
        goto trigger_cleanup;
    }


![](6.png)


    pTrigger = (Trigger*)sqlite3DbMallocZero(db, sizeof(Trigger));

构建触发器对象

    if (pTrigger == 0) goto trigger_cleanup;
    pTrigger->zName = zName;
    zName = 0;
    pTrigger->table = sqlite3DbStrDup(db, pTableName->a[0].zName);
    pTrigger->pSchema = db->aDb[iDb].pSchema;
    pTrigger->pTabSchema = pTab->pSchema;
    pTrigger->op = (u8)op;
    pTrigger->tr_tm = tr_tm == TK_BEFORE ? TRIGGER_BEFORE : TRIGGER_AFTER;
    pTrigger->pWhen = sqlite3ExprDup(db, pWhen, EXPRDUP_REDUCE);
    pTrigger->pColumns = sqlite3IdListDup(db, pColumns);
    assert(pParse->pNewTrigger == 0);
    pParse->pNewTrigger = pTrigger;
    ……
    sqlite3DbFree(db, zName);
    sqlite3SrcListDelete(db, pTableName);
    sqlite3IdListDelete(db, pColumns);
    sqlite3ExprDelete(db, pWhen);
    if (!pParse->pNewTrigger){
    sqlite3DeleteTrigger(db, pTrigger);
    }
    else{
    assert(pParse->pNewTrigger == pTrigger);
    }
    ……

    void sqlite3FinishTrigger(……){
    ……

如果我们没有初始化，则创建sqlite_master入口

    if (!db->init.busy){
    	Vdbe *v;
    	char *z;
    	v = sqlite3GetVdbe(pParse);
    	if (v == 0) goto triggerfinish_cleanup;
    	sqlite3BeginWriteOperation(pParse, 0, iDb);
    	z = sqlite3DbStrNDup(db, (char*)pAll->z, pAll->n);

在sqlite_master表中创建一个入口

    	sqlite3NestedParse(pParse,"INSERT INTO %Q.%s VALUES('trigger',%Q,%Q,0,'CREATE TRIGGER %q')",
    	db->aDb[iDb].zName, SCHEMA_TABLE(iDb), zName,pTrig->table, z);
    	sqlite3DbFree(db, z);
    	sqlite3ChangeCookie(pParse, iDb);
    	sqlite3VdbeAddParseSchemaOp(v, iDb,
    	sqlite3MPrintf(db, "type='trigger' AND name='%q'", zName));
    }
    if (db->init.busy){
    	……
    }

清除这个finish触发器

    triggerfinish_cleanup:
    sqlite3DeleteTrigger(db, pTrig);
    assert(!pParse->pNewTrigger);
    sqlite3DeleteTriggerStep(db, pStepList);
    }……

