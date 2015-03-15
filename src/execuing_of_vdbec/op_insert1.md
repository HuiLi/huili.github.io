# OP_Insert1
下图是OP_Insert的内容:

 ![]( 4-5-30.jpg)
 ```
case OP_Insert : {
	 Mem *pData= &aMem[pOp->p2];
	 VdbeCursor *pC =p->apCsr[pOp->p1];
	 pKey = &aMem[pOp->p3];
	 sqlite3BtreeInsert(pC->pCursor, 0, iKey,
                          pData->z, pData->n, nZero,
                          (pOp->p5 & OPFLAG_APPEND)!=0, seekResult);
}	```
OP_Insert操作符中，sqlite3BtreeInsert函数根据p1的值确定要使用的游标，根据iKey确定新纪录的key=0，根据pData得到要插入的内容，根据p5的值来确定需要不需要后期扩展，在后端（BTree和Pager）把数据插入数据，这里插入的是我们创建的表的属性。pc加1为27，下一个要执行的是aOp[27] OP_Close。
