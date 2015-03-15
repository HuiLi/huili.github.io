# 函数sqlite3VdbeTransferError
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;将属于VDBE相关的错误代码和错误信息作为第一个参数一起传递给数据库句柄处理函数（因此在调用sqlite3_errcode() 和sqlite3_errmsg()两个函数能够得到相应的信息）。sqlite3VdbeTransferError函数并没有处理任何错误的代码和错误信息仅仅是将他们复制然后传递给数据库句柄。
