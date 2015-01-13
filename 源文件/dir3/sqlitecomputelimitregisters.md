# SQLite中computeLimitRegisters函数
computeLimitRegisters函数：将结果集中限定的数据，放到寄存器里，相当于只返回结果集的部分。如果限定的返回行数大于结果集，就全部返回。
<pre>static void computeLimitRegisters(Parse *pParse, Select *p, int iBreak){
	  Vdbe *v = 0;/*初始化vdbe变量*/
	  int iLimit = 0;/*初始化iLimit*/
	  int iOffset;/*声明iOffset*/
	  int addr1, n;
	  if( p->iLimit ) return;/*如果SELECT结构体中含有limit偏移量直接返回*/
</pre>
