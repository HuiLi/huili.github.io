# sqlite3GetVdbe函数
为一个上下文对象申请一个VDBE，进行处理这个上下文对象，如果没有申请成功，会  生成一个错误信息。
<pre>
  Vdbe *sqlite3GetVdbe(Parse *pParse){
	   Vdbe *v = pParse->pVdbe;/*声明一个虚拟数据库引擎*/
	    if( v==0 ){/*如果虚拟数据库引擎为空*/
	       v = pParse->pVdbe = sqlite3VdbeCreate(pParse->db);
	    /*那么使用当前的VDBE根据语法分析树创建一个虚拟数据库引擎，并赋值给  语法分析树中的pVdbe和局部变量v*/
        if( v ){ /*如果虚拟数据库引擎不为空*/
	        sqlite3VdbeAddOp0(v, OP_Trace);/*添加OP_Trace指令到VDBE，并返回这个指令的地址*/
	}
         return v;/*返回创建成功的VDBE*/
	}</pre>
