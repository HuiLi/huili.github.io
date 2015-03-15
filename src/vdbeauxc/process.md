# vdbe执行中的处理
（1）函数void sqlite3VdbeSetNumCols(Vdbe *p, int nResColumn){}和
int sqlite3VdbeSetColName( Vdbe *p,int idx,int var,const char *zName,void (*xDel)(void*) ){}
这两个函数用来设置结果列的数目和名称。vdbe在处理SQL请求的时候会产生多列的结果，这时就需要设置结果列的数目和名称，从而返回给用户一个明确的结果。第一个函数要在编译时设置，而不是在vdbe的处理过程中设置，如此以便sqlite3_column_count()能够在sqlite3_step()之前被一个SQL声明所调用。第二个函数必须要在qlite3VdbeSetNumCols()之后调用。
（2）函数static int vdbeCommit(sqlite3 *db, Vdbe *p){}
该函数用来提交事务并对日志文件进行操作。若果一个事务是活跃的，就将其提交。只有当所有数据库成功完成BtreeCommitPhaseOne()后才执行提交操作，如果在提交中出现了BtreeCommitPhaseOne()的一个调用失败，表明在删除或者截断一个日志文件的时候出现了IO错误。在这种情况下，放弃处理并返回一个错误。同时，在进行提交的时候，需要将事务中的每一个数据库文件的名字写入新的日志文件中，还要对主日志文件进行同步。
