# 初始化vdbe

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该功能由函数void sqlite3VdbeRewind(Vdbe *p) {}实现。 程序在处理SQL之前，需要先对vdbe进行初始化。
当vdbe被运行之后，使用该函数将虚拟机恢复到初始状态。Rewind函数主要执行了以下9个操作来将vdbe的各项参数初始化：
 <br> p->pc = -1;<br>
  p->rc = SQLITE_OK;<br>
  p->errorAction = OE_Abort;<br>
  p->magic = VDBE_MAGIC_RUN;<br>
  p->nChange = 0;<br>
  p->cacheCtr = 1;<br>
  p->minWriteFileFormat = 255;<br>
  p->iStatement = 0;<br>
  p->nFkConstraint = 0;<br>
从上面的参数可以看出，vdbe的初始化主要就是对内部的各种参数赋初值，从而使之回到初始状态。需要注意的是程序中至少要有一个操作码，即 assert( p->nOp>0 )。此外，还应尽早设置p->magic的值。
