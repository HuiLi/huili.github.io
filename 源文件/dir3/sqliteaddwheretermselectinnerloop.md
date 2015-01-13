# SQLite中使用addWhereTerm函数和selectInnerLoop函数
<br><br> addWhereTerm函数：拆分where子句中条件表达式列表，将条件语句添加到select语法树中。根据给出的条件，进行判断，如果结果是true返回，如果是false就不返回。
<pre><br> static void addWhereTerm(
 	  Parse *pParse,                  /*解析上下文*/
 	  SrcList *pSrc,                  /*表集合*/
 	  int iLeft,                      /*第一个连接的表索引*/
 	  int iColLeft,                   /*第一个表的列索引*/
 	  int iRight,                     /*表集合中第二个表的索引*/
 	  int iColRight,                  /*第二个表的列索引*/
 	  int isOuterJoin,                /*外连接为true*/
 	  Expr **ppWhere                  /*where子句表达式*/
 	)</pre>
<br> <br> selectInnerLoop函数：这个函数产生的代码是为了select内连接循环。 <br> selectInnerLoop主要为了生成输出结果列的opcode。而且在生成最优的结果列的操作码之前，SELECT语法树先在sqlite3WhereBegin()中进行查询优化处理。如果取数据的表和列都是0，然后提取数据的列表达式列表被认为需要得到这一行的数据。如果srcTab（源表）中列数大于0，数据只从srcTab（源表）和pElist（被提取值列表）得到每一列的数据类型。
<pre>static void selectInnerLoop(
	  Parse *pParse,         /*解析上下文*/
 	  Select *p,             /*声明select查询结构体*/
 	  ExprList *pEList,       /*被提取的值列表*/
	  int srcTab,             /*提取数据的表*/
 	  int nColumn,           /*源表的行数*/
 	  ExprList *pOrderBy,     /*如果不是空，对结果排序使用这个关键词*/
	  SelectDest *pDest,      /*处理结果集*/
 	  int iContinue,          /*跳过此处到下一行*/
	)</pre>

<img src="../image/addWhereTerm.png">
