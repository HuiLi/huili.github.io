# static void autoIncStep()
输入参数：Parse *pParse, int memId, int regRowid
返回值：static void
函数作用：为一个自增计算器跟新最大的行Id，这个例程当前这个堆栈的顶端即将插入一个新的行id时运行。如果这个新的行Id比在MemId内存单元中的最大行id大时，那么这个内存单元将被更新。这个堆栈  是不可改变的。
解释：memID是内存中的ID的值；调用Vdbeaux.c文件中的sqlite3VdbeAddOp2函数。
