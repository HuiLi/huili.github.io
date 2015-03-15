# OP_NewRowid
下图是OP_NewRowid的内容:

 ![]( 4-5-13.jpg)
 ```
case OP_NewRowid : {
	i64 v=0;
	int res=0;
	VdbeCursor *pC= p->apCsr[pOp->p1];;
  	sqlite3BtreeLast(pC->pCursor, &res);
	if( res ){ v = 1; }
}```
OP_NewRowid操作符中的sqlite3BtreeLast函数会移动游标到表最后输入数据的地方，但是由于是空表，所以返回res为1，那么v=1，及rowid为1，并把v赋值给p2。pc加1为10，下一个要执行的是aOp[10] OP_Null。
