# Loadext及clegacy.c文件解析
1．介绍

它是加载数据库Sqlite扩展库的函数集合。Loadext用到的核心函数load_extension（X，Y）函数加载的SQLite扩展了使用入口点Y名为X的共享库文件。如果省略y，则默认入口点名称被使用，即zProc=0。如果扩展无法加载或初始化失败，load_extension（）函数会引发异常。
如果将尝试修改或删除SQL 函数或校验顺序，指定load_extension( )函数将会失败。这个扩展函数可以添加新功能或扩展文件，但无法修改或删除现有功能或校验顺序 , 因为这些函数和校验可能在其他地方使用当前运行的SQL 语句。加载扩展的更改或删除这个函数或者校验顺序, 请使用sqlite3_load_extension( )c语言 API.

2．实现

static const sqlite3_api_routines sqlite3Apis（）
该函数的结构包含指向所有SQLite的API例程的指针，这个结构被传递到扩展中以至于可以调用的SQLite库。当增加新的API，将它们添加到这个结构的底部为了保持向后兼容。使用新的API扩展，应首先调用sqlite3_libversion_number（）以确保该API，他们拟将由库支持。扩展还应该检查以确保该指针的函数不调用它之前为空。每个版本新增的API都会被添加到这个指针中。


定义sqlite3loadextention，这个接口会从指定的文件中加载SQLite扩展库。
尝试加载包含在文件zFile的SQLite扩展库。入口点是zProc。zProc可以是0，在一个默认入口点名称（sqlite3_extension_init）被使用的情况下，建议使用默认的名称。返回SQLITE_OK成功和SQLITE_ERROR出现错误。如果发生错误，pzErrMsg不为0，然后填写* pzErrMsg与错误消息文本。调用函数应该通过调用sqlite3DbFree来释放此内存。

为了避免旧的应用程序重新链接对新版本的SQLite的造成安全问题，运行load_extension，其默认关闭的。
必须调用sqlite3_enable_load_extension（）打开扩展负载。否则，会产生未授权错误。

追加新的共享库句柄DB->扩展阵列
  aHandle = sqlite3DbMallocZero(db, sizeof(handle)*(db->nExtension+1));
  if( aHandle==0 ){
    return SQLITE_NOMEM;
  }
  if( db->nExtension>0 ){
    memcpy(aHandle, db->aExtension, sizeof(handle)*db->nExtension);
  }
  sqlite3DbFree(db, db->aExtension);
  db->aExtension = aHandle;
  db->aExtension[db->nExtension++] = handle;
  return SQLITE_OK;

void sqlite3CloseExtensions(sqlite3 *db)
当数据库连接被关闭时会触发该函数，该函数用来清理加载扩展项 ，因为连接使用数据库时，会加载大量的扩展项，占用大量的资源，关闭与数据库连接后，不再使用，应调用其及时清理。

int sqlite3_enable_load_extension(sqlite3 *db, int onoff)
调用这个函数来启用或禁用扩展负载。扩展加载默认是禁用的，以免在旧的应用程序打开安全漏洞。
onoff是用来判断的是否禁用的标志。

#ifdef SQLITE_OMIT_LOAD_EXTENSION
static const sqlite3_api_routines sqlite3Apis = { 0 };
#endif
自动扩展代码必须加上无论扩展装载是否支持。如果经常扩展装载不可用，我们需要一个虚拟sqlite3Apis指针的代码。

static SQLITE_WSD struct sqlite3AutoExtList{
 int nExt;
  void (**aExt)(void);
} sqlite3Autoext = { 0, 0 };
定义一个具有自动加载的扩展列表的对象。 这个列表是跨线程共享的。其中的SQLITE_MUTEX_STATIC_MASTER互斥必须在访问这个列表时进行。

int sqlite3_auto_extension(void (*xInit)(void))
此接口使xInit（）函数用于创建的每个新的数据库连接调用。xInit（）为入口点的静态链接SQLite的扩展，将被自动加载到所有新的数据库连接。

void sqlite3_reset_auto_extension(void)
重置自动扩展加载机制。

void sqlite3AutoLoadExtensions(sqlite3 *db)
加载所有自动扩展。
如果出现任何错误，在数据库连接设置错误。

使用SQLite扩展函数来定义自己的函数
sqlite有个不方便的地方就是不能在sql中定义枚举量和常量。因为sqlite可以让用户自己扩展sql函数，所以我们可以根据自己的需要写出适合自己程序的函数。
当程序设计中需要很多枚举量的时候，比如道路表中道路类型列就是枚举量：0表示高速，1表示国道，2表示省道，3表示县道等，又不能在sql中定义这些枚举量，我们该怎么解决呢？
通过搜索引擎知道sqlite可以让用户自己扩展sql函数，可以使用sqlite扩展函数来解决这个问题。

sqlite扩展sql函数的方法和步骤：首先按sqlite扩展函数的原型定义自己的函数，然后使用sqlite3_create_function注册到sqlite中，然后就可以在sql中调用自己定义的函数，就跟使用预定义好的sqlite核心函数一样。


当程序设计中需要很多枚举量的时候，比如道路表中道路类型列就是枚举量：0表示高速，1表示国道，2表示省道，3表示县道等，又不能在sql中定义这些枚举量，我们该怎么解决呢？
通过搜索引擎知道sqlite可以让用户自己扩展sql函数，感觉可以使用sqlite扩展函数来解决这个问题。sqlite扩展sql函数的方法和步骤：首先按sqlite扩展函数的原型定义自己的函数，然后使用sqlite3_create_function注册到sqlite中，然后就可以在sql中使用自己定义的函数了，就跟使用预定义好的sqlite核心函数一样。

我们可以把高速类型定义成一个扩展函数，这个函数返回0：
 static void fun_ROAD_TYPE_HIGHWAY( sqlite3_context * context, int argc, sqlite3_value ** value ) { sqlite3_result_int64( context, 0 ); };
然后把它注册到sqlite中,注册名为ROAD_TYPE_HIGHWAY：
 sqlite3_create_function( db, "ROAD_TYPE_HIGHWAY", 0, SQLITE_ANY, 0, fun_ROAD_TYPE_HIGHWAY, 0, 0 )
注册完后我们就可以在sql语句中通过注册名ROAD_TYPE_HIGHWAY来使用这个函数了，如：选出所有高速的sql可以写成select * from road_table where road_type = ROAD_TYPE_HIGHWAY()

3．legacy.c分析

SQLite库的主文件。这个文件中的例程使程序接口对库生效。其他文件中的例程被SQLite内部使用，而不应该使库中的其他用户获得权限。
执行SQL代码。返回SQLITE_ success/failure码中的其中一个。从malloc函数中得到错误信息，并且写入到内存，并且使*pzErrMsg指针指向那个错误信息。
如果是查询语句，对查询结果的每一行调用xCallback()函数，pArg成为xCallback()函数的第一个论证。如果xCallback为空，即使有查询，也不调用callback函数
int sqlite3_exec(             /* sqlite3执行函数*/
  sqlite3 *db,                /* The database on which the SQL executes SQL的执行数据库*/
  const char *zSql,           /* The SQL to be executed 被执行的SQL*/
  sqlite3_callback xCallback, /* Invoke this callback routine 唤醒callback例程*/
  void *pArg,                 /* First argument to xCallback() xCallback()的第一个参数*/
  char **pzErrMsg             /* Write error messages here 错误信息写入**pzErrMsg*/
){
  int rc = SQLITE_OK;         /* Return code 返回码*/
  const char *zLeftover;      /* Tail of unprocessed SQL 跟踪未被执行的SQL*/
  sqlite3_stmt *pStmt = 0;    /* The current SQL statement 当前SQL的状态*/
  char **azCols = 0;          /* Names of result columns 结果列的名称*/
  int nRetry = 0;             /* Number of retry attempts 重试次数*/
  int callbackIsInit;         /* True if callback data is initialized 初始化callback*/

for(i=0; i<nCol; i++){
            azCols[i] = (char *)sqlite3_column_name(pStmt, i);
assert( azCols[i]!=0 );
          }
	sqlite3VdbeSetColName函数安装了UTF8字符名称列，所以sqlite3_column_name()函数不会失败
