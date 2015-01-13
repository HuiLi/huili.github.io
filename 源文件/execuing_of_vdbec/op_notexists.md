# OP_NotExists
下图是OP_NotExists的内容:

 ![]( 4-5-21.jpg)
 ```
case OP_NotExists : {
	VdbeCursor *pC = p->apCsr[pOp->p1];
	BtCursor *pCrsr = pC->pCursor;
	res = 0;
  	iKey = pIn3->u.i;//iKey为1
	sqlite3BtreeMovetoUnpacked(pCrsr, 0, iKey, 0, &res);
	pC->seekResult = res;
}```
OP_NotExists操作符根据sqlite3BtreeMovetoUnpacked函数执行的结果来确定是否之后或者之前还有操  作码，有的话res为0，所以可以继续执行下去，不用跳转到p2指向的操作码去。pc加1为18，下一个要执行的是aOp[18] OP_Rowid。
