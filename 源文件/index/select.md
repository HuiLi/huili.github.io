# 索引的删除
使用函数sqlite3DropIndex(Parse *pParse, SrcList *pName, int ifExists)删除索引。在这个函数中调用方法sqlite3FindIndex(db, pName->a[0].zName, pName->a[0].zDatabase)找到索引，使用函数sqlite3ClearStatTables(pParse, iDb, "idx", pIndex->zName)生成代码从而从主表中删除索引 。
