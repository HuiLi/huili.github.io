# OP_OpenWrite
下图是OP_OpenWrite的内容:

 ![]( 4-5-19.jpg)
 ```
case OP_OpenWrite : {
	int nField = pOp->p4.i;
  	int p2 = pOp->p2, iDb = pOp->p3; ;
	Db pDb = &db->aDb[iDb];//存储临时表的数据库
  	Btree* pX = pDb->pBt;
	int wrFlag = 1;
	VdbeCursor* pCur = allocateCursor(p, pOp->p1, nField, iDb, 1);
	pCur->pgnoRoot = p2;
	sqlite3BtreeCursor(pX, p2, wrFlag, pKeyInfo, pCur->pCursor);
	pCur->isTable = pOp->p4type!=P4_KEYINFO;
}```
在OP_OpenWrite操作符中，allocateCursor函数根据p3的值确定操作的是main数据库，根据p4确定分配的字段为5，根据p1的值确定这是写游标的索引，为VDBE实例v创建一个指向BTree游标，然后sqlite3BtreeCursor函数根据p2确定刚刚创建的表创建一个写游标，这个游标和为VDBE实例创建的游标是有联系的。pc加1为16，下一个要执行的是aOp[16] OP_MustBeInt。
