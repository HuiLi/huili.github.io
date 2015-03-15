# 析构函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数VdbeOp *sqlite3VdbeTakeOpArray(Vdbe *p， int *pnOp， int *pnMaxArg)
该函数返回vdbe的操作码数组的指针。它可以被vdbeFreeOpArray()调用来释放操作码数组的空间。在返回前，参数pnOp被设置为返回数组中的元素数量。pnMaxArg被设置为当前数组中的最大值。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数static void freeEphemeralFunction(sqlite3 *db， FuncDef *pDef)
如果参数pDef所指向的FuncDef结构为临时结构，那么函数将释放其所占的空间，否则什么也不做。
函数static void vdbeFreeOpArray(sqlite3 *db， Op *aOp， int nOp)
释放指针aOp以及与之相关的任何P4值所占的空间。该函数假定aOp不为空且包含有nOp条记录，需调用函数freeP4。
函数static void freeP4(sqlite3 *db， int p4type， void *p4)
删除P4的值，其中需要根据P4的类型进行不同的处理。
函数void sqlite3VdbeLinkSubProgram(Vdbe *pVdbe， SubProgram *p)
将子程序p传递给vdbe，并将其加入到vdbe的子程序链中，该链用于VM销毁时销毁所有与之有关的子程序。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
函数void sqlite3VdbeLeave(Vdbe *p)
该函数功能是解锁所有在sqlite3VdbeEnter加锁的B树。
函数static void releaseMemArray(Mem *p， int N)
该函数实现了释放N个内存元素功能。该函数中有个内联模块。这样处理的优势在于释放动态资源前内存单元值就已经被设为空值。
在实际测试中如果这样处理CPU(x86 linux，gcc version 4.1.2，-O6)会达到4.7%峰值，对于-O2，峰值会达到6.6%。测试示例是使用单个预处理Insert语句绑定和重设操作来插入1000行记录到一个没有索引的表中。插入操作作为一个事务处理。
