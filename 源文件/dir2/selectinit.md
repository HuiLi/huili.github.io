# 生成，清空，删除SELECT查询树函数
sqlite3Select函数：这是将SELECT查询树，相当于一个实体类,能将select查询树  转化为vdbe字节码，再让vdbe处理这个select查询树。
<pre>
int sqlite3Select(
	 Parse *pParse,         /*解析上下文*/
	 Select *p,             /*编写SELECT语句*/
	 SelectDest *pDest      /*处理查询结果*/
	){
	 int i, j;              /*循环计数器*/
	 WhereInfo *pWInfo;     /*从sqlite3WhereBegin返回*/
	 Vdbe *v;               /*正在构建的虚拟机*/
	 int isAgg;             /*如果有像count(*)的查询列表为true*/
	 ExprList *pEList;      /*提取出的列组成的列表*/
	 SrcList *pTabList;     /*SELECT的来源表*/
	 Expr *pWhere;          /*WHERE子句，可能是空*/
	 ExprList *pOrderBy;    /* ORDER BY 子句，可能是空*/
	 ExprList *pGroupBy;    /* GROUP BY 子句，可能是空*/
	 Expr *pHaving;         /*HAVING子句，可能是空*/
	 int isDistinct;        /*出现DISTINCT关键字为true*/
	 int distinct;          /*表中不重复设置*/
	 int rc = 1;            /*函数的返回值*/
	 int addrSortIndex;     /*OP_OpenEphemeral指令地址*/
	 int addrDistinctIndex; /*OP_OpenEphemeral指令地址*/
	 AggInfo sAggInfo;      /*聚集函数信息*/
	 int iEnd;              /*结束查询的地址*/
	 sqlite3 *db;           /*数据库连接*/
}</pre>
<br><br>sqlite3SelectNew函数：声明一个SELECT结构体，里面包含SELECT查询树  中的from，on,where,group by,having等子句。
<pre>Select *sqlite3SelectNew(
	  Parse *pParse,        /*解析上下文*/
	  ExprList *pEList,    /*声明一个表达式列表，用来放表达式 */
	  SrcList *pSrc,        /*放FROM子句，扫描有哪些表 */
	  Expr *pWhere,         /*声明子句表达式放where子句*/
	  ExprList *pGroupBy,   /*声明表达式列表放Group by子句表达式*/
	  Expr *pHaving,       /*声明一个表达式放Having表达式*/
	 ExprList *pOrderBy,  /*声明表达式列表放Order by子句表达式*/
	 int isDistinct,       /*声明是否使用distinct关键字，官方文档说默认不用*/
	  Expr *pLimit,         /*声明一个表达式放Limit子句表达式 */
	  Expr *pOffset         /*声明一个表达式放Offset偏移量子句表达式*/
	)</pre>
<br><br>sqlite3SelectDestInit函数：处理SELECT结果集，主要和VEDB交互。
<pre>void sqlite3SelectDestInit(SelectDest *pDest, int eDest, int iParm){
	  pDest->eDest = (u8)eDest;/*u8是一个无符号字型，eDest是为了处理select操作结果*/
	  pDest->iSDParm = iParm;/*eDest的第几个处理方法*/
	  pDest->affSdst = 0;/*	相当于设置eDest==SRT_Set，默认为0，表明没有设置*/
	  pDest->iSdst = 0;/*结果写在基址寄存器的编号，默认为0*/
	  pDest->nSdst = 0;/*分配寄存器的数量*/
	}</pre>
sqlite3SelectDelete函数：先清空内存中SELECT结构体的内容，再删除。
<pre><br><br>void sqlite3SelectDelete(sqlite3 *db, Select *p){/*传入数据库连接和Select结构体*/
	  if( p ){ /*如果Select结构体存在*/
		clearSelect(db, p);/*先清空Select结构体中内容*/
		sqlite3DbFree(db, p);/*再释放Select结构体*/
	  }
	}
</pre>
