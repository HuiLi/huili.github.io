# OP_Copy
下图是OP_Copy的内容:

 ![]( 4-5-27.jpg)
 ```
 case OP_Copy : {
	int n = pOp->p3;
  	pIn1 = &aMem[pOp->p1];
  	pOut = &aMem[pOp->p2];
	sqlite3VdbeMemShallowCopy(pOut, pIn1, MEM_Ephem);
}
```
OP_Copy操作符中sqlite3VdbeMemShallowCopy函数把p1指向内存的内容复制到了p2指向的内存中。pc加  1为24，下一个要执行的是aOp[24] OP_String。

