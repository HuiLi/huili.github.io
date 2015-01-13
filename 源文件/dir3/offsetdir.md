# OFFSET子句
codeOffset函数：一般与limit结合使用。先确定offset的偏移量，得出返回结果集的起始位置，再根据limit返回限定大小的结  果集。
<pre>static void codeOffset(
	  Vdbe *v,          /*生成代码给虚拟机*/
	  Select *p,        /*声明Select结构体*/
	  int iContinue     /*从这里跳过当前记录*/
	){
	  if( p->iOffset && iContinue!=0 ){/*如果Select结构体中含有IOffset属性值并且设置了跳过当前记录*/
		int addr;
		sqlite3VdbeAddOp2(v, OP_AddImm, p->iOffset, -1);/*在VDBE中新添加一条指令，返回一个新指令的地址*/
		addr = sqlite3VdbeAddOp1(v, OP_IfNeg, p->iOffset);/*实质上调用sqlite3VdbeAddOp3（）修改指令的地址*/
		sqlite3VdbeAddOp2(v, OP_Goto, 0, iContinue);/*设置跳往的地址*/
		VdbeComment((v, "skip OFFSET records"));/*输入偏移量*/
		sqlite3VdbeJumpHere(v, addr);/*输入偏移地址，改变下一条的执行地址*/
	  }
	}</pre>
