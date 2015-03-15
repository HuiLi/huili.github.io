# 函数sqlite3VdbeFinalize
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;在程序执行过后清除VDBE占用的资源并删除这个VDBE。最后返回的代码是一个实数。将整个过程中所有的错误信息传递给指针参数pzErrMsg。
2.1.6 sqlite3VdbeDeleteAuxData(VdbeFunc *pVdbeFunc, int mask)为每一个VdbeFunc中的AuxData调用析构函数释放资源而且在mask中有与之对应的位是清楚的。如果VdbeFunc中nAux的值超过31时就会调用xDelete函数来删除实例。当mask参数值为0时会删除所有的AuxData实例。
