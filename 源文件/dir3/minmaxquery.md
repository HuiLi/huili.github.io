# minMaxQuery函数
如果select语句中包含max或min查询，直接返回WHERE_ORDERBY_MAX或WHERE_ORDERBY_MIN。如果不存在，返回0.
<pre>	static u8 minMaxQuery(Select *p){
	  Expr *pExpr; /*声明一个表达式*/
	  ExprList *pEList = p->pEList;/*将SELECT的表达式列表赋值给表达式列表pEList*/
	  if( pEList->nExpr!=1 ) return
	  WHERE_ORDERBY_NORMAL;/*如果表达式列表中的表达式个数不为1，返回WHERE_ORDERBY_NORMAL（默认）*/
	  pExpr = pEList->a[0].pExpr;/*靖表达式列中的第一个表达式赋值给pExpr*/
	  if( pExpr->op!=TK_AGG_FUNCTION ) return 0;/*如果表达式的操作不是TK_AGG_FUNCTION，直接返回0*/
	  if( NEVER(ExprHasProperty(pExpr, EP_xIsSelect)) ) return 0;/*如果表达式pExpr中含有EP_xIsSelect，返回0*/
	  pEList = pExpr->x.pList;/*将表达式中x的表达式列表赋值给pEList*/
	  if( pEList==0 || pEList->nExpr!=1 ) return 0;/*如果pEList为空或者表达式列表中表达式个数不为1，直接返回0*/
	  if( pEList->a[0].pExpr->op!=TK_AGG_COLUMN ) return
	  WHERE_ORDERBY_NORMAL;/*如果表达式中操作不是TK_AGG_COLUMN，返回WHERE_ORDERBY_NORMAL（默认）*/
	  assert( !ExprHasProperty(pExpr, EP_IntValue) );/*插入断点，如果表达式pExpr中不含EP_xIsSelect，抛出错误信息*/
	  if( sqlite3StrICmp(pExpr->u.zToken,"min")==0 ){/*如果解析表达式中间字符串为min，返回WHERE_ORDERBY_MIN（最小值）*/
		return WHERE_ORDERBY_MIN;
	  }else if( sqlite3StrICmp(pExpr->u.zToken,"max")==0 ){/*如果解析表达式中间字符串为max，返回WHERE_ORDERBY_MAX（最大值）*/
		return WHERE_ORDERBY_MAX;
	  }
	  return WHERE_ORDERBY_NORMAL;/*最后返回WHERE_ORDERBY_NORMAL（默认）*/
	}
</pre>
