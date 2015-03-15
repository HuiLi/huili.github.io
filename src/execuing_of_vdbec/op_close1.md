# OP_Close
下图是OP_Close的内容:

 ![]( 4-5-16.jpg)
 ```
 case OP_Close : {
	 sqlite3VdbeFreeCursor(p, p->apCsr[pOp->p1]);
  	 p->apCsr[pOp->p1] = 0;
}
```
操作和上一步一模一样，这里是用来重复确认是否释放游标。pc加1为14，下一个要执行的是aOp[14] OP_Null。。
