# OP_Halt
下图是OP_Halt的内容:

 ![]( 4-5-35.jpg)
 ```
 case OP_Halt : {
	p->rc = pOp->p1;
  	p->errorAction = (u8)pOp->p2;
  	p->pc = pc;
	rc = sqlite3VdbeHalt(p);
	sqlite3VdbeLeave(p);
}
```

OP_Halt操作符就是对VDBE操作的强制关闭，然后把VDBE释放。至此vdbe_exec() 函数执行结束。
