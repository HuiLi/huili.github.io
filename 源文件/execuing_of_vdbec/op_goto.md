# OP_Goto
下图是OP_Goto的内容:

 ![](4-5-4.jpg)
```
case OP_Goto: {
	pc = pOp->p2 - 1;
}```
OP_Goto操作符就是跳转到p2所指向的操作码，这里是pc为0，经过for循环后pc为1。下一个要执行的是  aOp[1] OP_ReadCookie。
