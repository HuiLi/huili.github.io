# OP_Null
下图是OP_Null的内容:

 ![]( 4-5-18.jpg)
 ```
 case OP_Null : {
	 int cnt;
	 cnt = pOp->p3 - pOp->p2; //cnt=-3
	while( cnt>0 ){
	….}
}
```

这一次p3>p2，进入while循环，sqlite3VdbeMemSetNull函数把p2设置为NULL。pc加1为15，下一个要执行的是aOp[15] OP_OpenWrite。
