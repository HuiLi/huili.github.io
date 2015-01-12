# 验证数据库中表的列信息是否可访问
3.1函数头

int sqlite3AuthReadCol(
 Parse *pParse,       /* 解析上下文*/
 const char *zTab,   /* 表名*/
  const char *zCol,      /* 列名 */
  int iDb         /* 包含数据库的索引。*/
)

3.2工作流程

（1）将授权函数赋值给授权回调rc，判断rc==SQLITE_DENY是否成立，若成立，则判断db->nDb>2 或 iDb!=0是否满足，若满足返回错误信息，授权访问数据库，表名，列名被禁止，若不满足返回错误信息，授权访问表名，列名被禁止。

（2）若rc==SQLITE_DENY成立，如果rc!=SQLITE_IGNORE && rc!=SQLITE_OK保持，则调用sqliteAuthBadReturnCode(pParse)函数。

（3）进行授权后，返回授权访问的数据库中表的列信息rc

如图为AuthReadCol函数实现流程

<img src='AuthReadCol函数.jpg'>
