# OP_Rowid
下图是OP_Rowid的内容:

 ![]( 4-5-22.jpg)
 ```
case OP_Rowid : {
	VdbeCursor *pC = p->apCsr[pOp->p1];
	sqlite3VdbeCursorRestore(pC);
	sqlite3BtreeKeySize(pC->pCursor, &v);
}```
OP_Rowid操作符中sqlite3VdbeCursorRestore函数恢复p1当前指向的游标赋值给pC，然后使用sqlite3BtreeKeySize函数把p1指向的表的行号存入p2中。pc加1为19，下一个要执行的是aOp[19] OP_IsNull。
