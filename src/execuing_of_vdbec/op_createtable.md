# OP_CreateTable
下图是OP_CreateTable的内容:

 ![]( 4-5-11.jpg)
 ```
case OP_CreateTable: {
	int pgno=0,
	int flags = BTREE_INTKEY;//1
	Db* pDb = &db->aDb[pOp->p1];
  	sqlite3BtreeCreateTable(pDb->pBt, &pgno, flags);
	pOut->u.i = pgno;//pgno是新表的根页号
}```
OP_CreateTable操作符根据p1的值0确定是在main数据库中添加新表，sqlite3BtreeCreateTable函数就  是在后端（BTree和Pager）进行表的创建，并返回新表所在的根页号给p2。pc加1为8，下一个要执行的是aOp[8] OP_OpenWrite。
