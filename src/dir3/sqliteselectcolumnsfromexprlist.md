# SQLite中FROM子句的实现
在SQLite中FROM子句功能是由函数selectColumnsFromExprList函数实现的.<br>
selectColumnsFromExprList函数将将from后跟的表达式列表拆分，然后根据连接类型，调用连接函数，对表进行连接。<br>
注：连接主要看on部分，from默认连接方式  只有一种，采用inner join的方式。<br>
该函数全文如下:
<pre>
static int selectColumnsFromExprList(
	  Parse *pParse,          /*解析上下文*/
	  ExprList *pEList,       /*来自于列名的表达式列表*/
	  int *pnCol,             /*写列名*/
	  Column **paCol          /*写出新的列表*/
	){
	   sqlite3 *db = pParse->db;   /*声明一个数据库连接*/
	   int i, j;                   /*循环计数*/
	   int cnt;                    /*索引添加产生唯一的名字*/
       Column *aCol, *pCol;        /*循环结束的结果列*/
	   int nCol;                   /*结果集中列的总数*/
	   Expr *p;                    /*一个单独结果列的表达式*/
       char *zName;                /*列名*/
       int nName;                  /*存放列名的数组的长度*/
       return SQLITE_OK;           /*返回SQLITE_OK值*/
}
	</pre>

