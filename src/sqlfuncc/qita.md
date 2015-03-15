# 其他函数
5.1数据的类型

（1）static void typeofFunc(sqlite3_context *context, int NotUsed,  sqlite3_value**argv)

返回参数类型

（2）工作流程

	若参数是SQLITE_INTEGER，则返回整数integer

	若参数是SQLITE_TEXT，则返回文本text

	若参数是SQLITE_FLOAT，则返回浮点数real

	若参数是SQLITE_BLOB，则返回二进制大对象blob

	若参数与以上的类型都不匹配，则返回空

如图为typeofFunc函数实现流程

<img src='typeofFunc函数.jpg'>

5.2 当前数据库连接最后插入行的ROWID

static void last_insert_rowid(  sqlite3_context *context,  int NotUsed,  sqlite3_value **NotUsed2)

该函数调用函数last_insert_rowid()SQL 函数将被添加sqlite3_last_insert_rowid( )C/C++ 接口函数.返回最后一个连接从数据库插入的行

5.3所运行的SQLite库的版本号

static void versionFunc( sqlite3_context *context,  int NotUsed,sqlite3_value **NotUsed2)

这个函数是一个关于sqlite3_libversion()c接口的SQL封装，结果是SQLite库正在运行的版本。
