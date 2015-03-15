# debug模式
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果需要调试功能，设置sqlite3VdbeAddopTrace=1即可。在这种情况下，所有操作码(opcodes)都会被添加到指令流，以便于显示出来。
（1）操作码迭代器部分
以下是一个Vdbe迭代器结构的定义
struct VdbeOpIter {<br>
  Vdbe *v;                   /* Vdbe to iterate through the opcodes of */<br>
  SubProgram **apSub;        /* Array of subprograms */<br>
  int nSub;                  /* Number of entries in apSub */<br>
  int iAddr;                 /* Address of next instruction to return */<br>
  int iSub;                  /* 0 = main program， 1 = first sub-program etc。 */<br>
};<br>
函数static Op *opIterNext(VdbeOpIter *p)
该函数实现了操作码迭代器的next功能，可以通过以下方式使用：
Op *pOp;<br>
 VdbeOpIter sIter;<br>
 memset(&sIter， 0， sizeof(sIter));<br>
sIter.v = v;                         // v is of
<br>
type Vdbe*     type1: P4_SUBPROGRAM<br>
while( (pOp = opIterNext(&sIter)) ){<br>
  // Do something with pOp
  <br>
   }<br>
sqlite3DbFree(v->db， sIter.apSub);<br>
（2）显示部分
操作码追踪功能由函数void sqlite3VdbeTrace(Vdbe *p， FILE *trace)实现.
trace是保存指令流的文件，通过访问该指针可以得到指令的执行过程。
打印单个操作码功能由函数void sqlite3VdbePrintOp(FILE *pOut， int pc， Op *pOp)实现。<br>
（3）操作部分
1）得到当前地址操作码
该功能功能由函数VdbeOp *sqlite3VdbeGetOp(Vdbe *p， int addr)实现。<br>
该函数实现了根据给定地址addr返回对应的操作码，如果地址为-1，那么返回最近插入的操作码。如果在调用该函数之前出现内存分配错误，那么将返回一个虚拟的操作码，尽管该操作码是一个可写的值，但是该操作码是只读的。返回的虚拟操作码可以保证在出现内存不足错误且不验证返回值是否为有效的情况下继续运行。但是由于虚拟操作码是0，该操作码不会被写入新值。这是由代码审查程序Valgrind验证通过的。
2）交换两个vdbe的所有内容
该功能由函数void sqlite3VdbeSwap(Vdbe *pA， Vdbe *pB)实现。
3）程序验证
该功能由函数int sqlite3VdbeAssertMayAbort(Vdbe *v， int mayAbort)实现
验证VM中的与pParse解析器相关联的程序是否出现ABORT异常。该异常由语句产生，但是该异常不需要所有事物都回滚。如果主程序或者子程序出现以下条件，那么该异常可能会发生。
*  OP_Halt with P1=SQLITE_CONSTRAINT and P2=OE_Abort。
*  OP_HaltIfNull with P1=SQLITE_CONSTRAINT and P2=OE_Abort。
*  OP_Destroy
*  OP_VUpdate
*  OP_VRename
*  OP_FkCounter with P2==0 (immediate foreign key constraint)
当ABORT异常发生，或者没有发生时验证解析器mayAbort域的值是否为真。如果匹配则返回真，否则返回假。这个函数将作为编译器中一个assert语句。它可以这样使用
assert( sqlite3VdbeAssertMayAbort(pParse->pVdbe， pParse->mayAbort) );
在该函数执行过程中，可能会出现内存分配失败的情况，当内存分配失败后，循环语句不会遍历所有的操作码。这样会导致hasAbort参数设置错误。因此在出现内存分配失败的情况时返回mallocFailed
4）修改操作码注释
该功能由函数static void vdbeVComment(Vdbe *p， const char *zFormat， va_list ap)
void sqlite3VdbeComment(Vdbe *p， const char *zFormat， …)
void sqlite3VdbeNoopComment(Vdbe *p， const char *zFormat， …)实现。
以上函数用于改变最近指令编码的注释。或者插入一个No-op并添加注释到这个新的指令中。这样便于调分辨编码的内容。
5）返回当前设置的SQL语句
该功能由函数const char *sqlite3_sql(sqlite3_stmt *pStmt)实现。
返回值保存在vdbe由pStmt转换而来。
