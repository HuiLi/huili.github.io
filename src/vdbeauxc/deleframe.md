# 删除vdbe结构体
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该功能由函数void sqlite3VdbeFrameDelete(VdbeFrame *p){ }实现。<br>
当程序运行完毕后，需要销毁VDBE。该函数嵌套调用了函数qlite3VdbeFreeCursor(p->v， apCsr[i])和函数qlite3VdbeFreeCursor(p->v， apCsr[i])。由函数的定义可以看出，在删除vdbe结构体时并不是将其真正的删除，而是先使用qlite3VdbeFreeCursor(p->v， apCsr[i])释放游标，接着调用releaseMemArray(aMem， p->nChildMem)释放Mem结构体数组，最终使用sqlite3DbFree(p->v->db， p)释放内存数组。
