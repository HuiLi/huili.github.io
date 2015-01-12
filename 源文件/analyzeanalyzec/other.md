# 其他用到的一些函数
8.1 打开sqlite_stat1表

static void openStatTable(

  Parse *pParse,          /* 解析上下文*/

  int iDb,                /* 正在分析的数据库*/

  int iStatCur,           /* 打开 sqlite_stat1 表的游标 */

  const char *zWhere,     /* 删除这个表或索引的条目 */

  const char *zWhereType  /* "tbl" 或 "idx" */
)

8.2 工作流程

	该函数的是用于打开sqlite_stat1表，在iStatCur游标位置进行写操作，如果库中有SQLITE_ENABLE_STAT3的宏定义，那么sqlite_stat3 表将被打开从iStatCur+1位置开始写。

	如果sqlite_stat1表之前不存在并且库中以SQLITE_ENABLE_STAT3宏定义编译的，那么该表被创建。

	参数zWhere可能是一个指向包含一个表名的缓存的指针，或者是一个空指针，如果不为空，那么所有在表sqlite_stat1和sqlite_stat3之中相关联的表的条目将被删除。

	如果zWhere==0,那么将删除所有stat表中的条目。
