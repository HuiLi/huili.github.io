# SQLite中sqlite3JoinType函数，setJoinExpr函数与sqliteProcessJoin函数
sqlite3JoinType预定义连接类型，setJoinExpr设置连接类型，sqliteProcessJoin处理连接的表。
<br> sqlite3JoinType函数：声明各种连接的类型。解析SELECT的连接操作符，再与该函数的连接类型对应起来。默认为内连接。
<pre> / 自己注：这个函数为了确定连接的种类。根据连接的关键字处理连接的类  型。返回的整数代表连接的类型，而在处理关键字的过程中检测到错误，
那也返回连接类型，默认的连接类型是inner join连接。
/.
 int sqlite3JoinType(Parse *pParse, Token *pA, Token *pB, Token *pC){/*传入分析树，三个令牌结构体.其中Parse是
 	** 语法解析器，分析截断文本，提出出单词。Token是子句的关键词*/
	  int jointype = 0;/*确定连接类型*/
 	  Token *apAll[3];/*存放令牌*/
 	  Token *p;/*声明一个临时令牌*/
					    /*   0123456789 123456789 123456789 123 */
	  static const char zKeyText[] =
	                        "naturaleftouterightfullinnercross";/*存放字符数组，里面装的是连接类型，在下文中进行调用*/
	  static const struct {/*声明一个内部结构体*/
	    u8 i;        /*zKeyText数组的起始关键字*/
	 	u8 nChar;    /*关键字的字符长度*/
	    u8 code;     /*连接类型标记*/
	   } aKeyword[] = {/*声明一个关键字数组，根据下标和长度找到连接类型*/
 	     /* natural */ { 0,  7, JT_NATURAL                },/*下标从0开始，长度为7，自然连接*/
	     /* left    */ { 6,  4, JT_LEFT|JT_OUTER          },/*下标从6开始，长度为4，左连接或外连接*/
 	     /* outer   */ { 10, 5, JT_OUTER                  },/*下标从10开始，长度为5，外连接*/
	 	 /* right   */ { 14, 5, JT_RIGHT|JT_OUTER         },/*下标从14开始，长度为5，右连接或外连接*/
	     /* full    */ { 19, 4, JT_LEFT|JT_RIGHT|JT_OUTER         },/*下标从19开始，长度为4，左连接或右连接或外连接，实质是个全连接*/
		 /* inner   */ { 23, 5, JT_INNER                  },/*下标从23开始，长度为5，内连接*/
	     /* cross   */ { 28, 5, JT_INNER|JT_CROSS         },/*下标从28开始，长度为5，内连接或CROSS连接，实质是个CROSS join*/
	  };
</pre>
<br> <br> setJoinExpr函数：根据上一函数，得到select的连接类型，再确定与哪个表进行连接及连接类型。
<pre>static void setJoinExpr(Expr *p, int iTable){/*传入一个表达式，一个待连接的表*/
 	  while( p ){
	 	ExprSetProperty(p, EP_FromJoin);/*设置join中使用ON和USING子句*/
		assert( !ExprHasAnyProperty(p, EP_TokenOnly|EP_Reduced) );/*判断表达式的属性，关于表达式的长度和剩余长度*/
		ExprSetIrreducible(p);/*调试表达式，判读是否错误*/
	 	p->iRightJoinTable = (i16)iTable;/*连接右表，即参数中传入的表*/
		setJoinExpr(p->pLeft, iTable);/*递归调用自身*/
	p = p->pRight;/*赋值表达式p为原来p的右子节点*/
	   }
	}
	</pre>

<br> <br> sqliteProcessJoin函数：进行表之间的连接，生成一个中间结果集。
<pre> static int sqliteProcessJoin(Parse *pParse, Select *p){/*传入分析树，Select结构体*/
	  SrcList *pSrc;                  /*添加所有的表到表集合中*/
 	  int i, j;                       /*循环参数*/
	  struct SrcList_item *pLeft;     /*被添加的左表*/
	  struct SrcList_item *pRight;    /*被添加的右表*/
 	  pSrc = p->pSrc;/*赋值pSrc为Select结构体中FROM子句属性*/
 	  pLeft = &pSrc->a[0];/*将表数组中第一个表作为左表*/
 	  pRight = &pLeft[1];/*将表数组中第二个表作为右表*/
</pre>
