# 与表相关的操作
外键：

 void sqlite3CreateForeignKey(

  Parse *pParse,        //解析上下文

  ExprList *pFromCol,   //创建这个表中指向另一表的列

  Token *pTo,          //创建新表的名字

  ExprList *pToCol,    //创建另一表的列指向

  int flags           //添加冲突解决算法的数目。

)

调用进程从当前正在建的表中创建一个新的外键约束。
void sqlite3DeferForeignKey(Parse *pParse, int isDeferred)对刚创建的外键做相应的调整。

表锁

void sqlite3TableLock(

	Parse *pParse,     /*索引上下文 */
	int iDb,           // 索引数据库包含表的锁
	int iTab,          //根表的页码是锁着的
	u8 isWriteLock,    //对一个写入锁是true
	const char *zName  //加锁的表名
	)
使用这个函数在表上建立写锁，并且调用遍历表的方法和表的约束方法。
Table *sqlite3FindTable(sqlite3 *db, const char *zName, const char *zDatabase)通过数据库名和表名找到table，然后返回Table。
