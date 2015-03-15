# OP_Integer
下图是OP_Integer的内容:

 ![]( 4-5-9.jpg)
 ```
 case OP_Integer: {         /* out2-prerelease */
  pOut->u.i = pOp->p1;
  break;
}
```
和上面一样，OP_Integer操作符就是把p1的值赋值给pOut，也就是p2。pc加1为6，下一个要执行的是aOp[6] OP_SetCookie。
