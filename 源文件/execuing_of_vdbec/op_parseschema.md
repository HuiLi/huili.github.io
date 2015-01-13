# OP_ParseSchema
下图是OP_ParseSchema的内容:

 ![]( 4-5-34.jpg)
 ```
case OP_ParseSchema : {
	iDb = pOp->p1;
	zMaster = SCHEMA_TABLE(iDb);
	zSql = sqlite3MPrintf(db,"SELECT name, rootpage, sql FROM '%q'.%s WHERE %s ORDER BY rowid",db->aDb[iDb].zName, zMaster, pOp->p4.z);
	sqlite3_exec(db, zSql, sqlite3InitCallback, &initData, 0);
}	```

OP_ParseSchema操作符把刚刚创建表的操作全部写入到sqlite_master表中，写入过程也是使用sqlite3_exec执行zSql语句。这是对数据库操作的记录，是必须的。pc加1为31，下一个要执行的是aOp[31] OP_Halt。
