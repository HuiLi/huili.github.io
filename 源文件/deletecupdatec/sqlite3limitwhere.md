# sqlite3LimitWhere
<pre>
 Expr *sqlite3LimitWhere(
  Parse *pParse,               /* The parser context */
  SrcList *pSrc,               /* the FROM clause -- which tables to scan */
  Expr *pWhere,                /* The WHERE clause.  May be null */
  ExprList *pOrderBy,          /* The ORDER BY clause.  May be null */
  Expr *pLimit,                /* The LIMIT clause.  May be null */
  Expr *pOffset,               /* The OFFSET clause.  May be null */
  char *zStmtType              /* Either DELETE or UPDATE.  For error messages. */
)
</pre>

此函数由如下几部分组成：
<pre>
1）order by没有limit就输出错误信息。
2）果执行LIMIT/OFFSET语句.我们只需要生成一个选择表达式。
3）如果LIMIT语句为空，OFFSET语句也为空。
4）生成一个选择表达式树来执行DELETE或UPDATE中的LIMIT/OFFSET语句。
5）当需要DELETE/UPDATE树和SELECT子树时，复制FROM子句。。
6）生成SELECT表达式树。
7）为DELETE/UPDATE生成新的WHERE ROWID IN子句。
8）出现错误，清理分配的东西。
</pre>
