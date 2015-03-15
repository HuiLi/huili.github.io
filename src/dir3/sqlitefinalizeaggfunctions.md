# SQLite中finalizeAggFunctions函数
<br>  finalizeAggFunctions函数：先处理聚集函数，再为AggInfo结构体中每一个聚集函数调用OP_AggFinalize（完成）操作。
<pre>static void finalizeAggFunctions(Parse *pParse, AggInfo *pAggInfo){
	    Vdbe *v = pParse->pVdbe;/*将语法解析树中的pVdbe赋值给v*/
	    int i;
	    struct AggInfo_func *pF;/*声明一个聚集函数功能结构体（AggInfo_func为AggInfo子结构体）*/
 	    for(i=0, pF=pAggInfo->aFunc; i<pAggInfo->nFunc; i++, pF++){/*遍历聚集函数*/
 		ExprList *pList = pF->pExpr->x.pList;/*将聚集函数的表达式赋值给pList*/
		assert( !ExprHasProperty(pF->pExpr, EP_xIsSelect) );/*插入断点，如果pE不含EP_xIsSelect，抛出错误信息*/
 		sqlite3VdbeAddOp4(v, OP_AggFinal, pF->iMem, pList ? pList->nExpr : 0, 0,
						  (void*)pF->pFunc, P4_FUNCDEF);/*添加一个OP_OpenEphemeral操作符，并将它的值作为一个指针*/
	  }
	}</pre>
