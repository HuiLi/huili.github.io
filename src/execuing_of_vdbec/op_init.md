# OP_Init
下图是OP_Init的内容:

![](4-5-1.jpg)

```
case OP_Init: {          /* jump */
  if( pOp->p2 ){
    pc = pOp->p2 - 1;
  }
}```
OP_Init操作符是根据p2的值去确定下一个操作符，这里p2=32，所以pc=31，经过for循环后，pc=32。那么下一个要执行的操作符是aOp[32]及OP_Transaction。
