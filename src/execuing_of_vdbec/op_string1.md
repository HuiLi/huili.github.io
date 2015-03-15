# OP_String1
下图是OP_String的内容:


 ![]( 4-5-25.jpg)
 ```
case OP_String : {
	 pOut->flags = MEM_Str|MEM_Static|MEM_Term;
  	pOut->z = pOp->p4.z;
  	pOut->n = pOp->p1;
  	pOut->enc = encoding;
}
```

和上一步操作一样，把p4中存放的字符串“table1”和存放p4长度6的p1都放在p2指向的内存中。pc加1为  22，下一个要执行的是aOp[22] OP_String。
