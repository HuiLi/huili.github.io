# codeDistinct函数
codeDistinct函数：找到一个未出现的数据，放到预分配好的内存里，最后输出满足distinct的记录。
<pre>static void codeDistinct(
	  Parse *pParse,     /*分析语法树并且生成上下文*/
	  int iTab,          /*排序索引用来检测唯一性*/
	  int addrRepeat,    /*如果没有“去除重复”跳到此处*/
	  int N,             /*元素的个数*/
	  int iMem           /*首元素*/
	){
	  Vdbe *v;
	  int r1;
	  v = pParse->pVdbe;/*声明一个处理数据库字节码的引擎*/
	  r1 = sqlite3GetTempReg(pParse);/*分配一个寄存器存储中间结果*/
	  sqlite3VdbeAddOp4Int(v, OP_Found, iTab, addrRepeat, iMem, N);/*把操作的值看成整数，然后添加这个操作符到虚拟机中*/
	  sqlite3VdbeAddOp3(v, OP_MakeRecord, iMem, N, r1);/*调用sqlite3VdbeAddOp3（）修改指令的地址*/
	  sqlite3VdbeAddOp2(v, OP_IdxInsert, iTab, r1);/*实际也是使用sqlite3VdbeAddOp3()只是参数变为前4个，修改指令的地址*/
	  sqlite3ReleaseTempReg(pParse, r1);/*解除寄存器,sqlite3GetTempReg()分配的*/
	}
</pre>
