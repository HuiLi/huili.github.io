# Main.c
# 函数功能

const char *sqlite3ErrStr(int rc)


	此函数主要作用:根据错位类型码返回对应的错误信息
	以下为一些错误码的示例

	当没有对应的错误码时，返回"unknown error"

busy handler函数

	sqlite提供的busy handler函数有两个，但对于一个连接来说，只能有一个busy handle，两个函数会相互影响，设置一个的同时会清除另一个，应根据需要来选择。



	int sqlite3_busy_handler(sqlite3 *db, int (*xBusy)(void*,int),void *pArg)
	int sqlite3_busy_timeout(sqlite3 *db, int ms)
    int sqlite3_busy_handler(…)


	三个参数: sqlite3 *db ,  int ( *xBusy)(void*, int),   void  *pArg
	不注册此函数时默认回调函数为NULL，清除busy handle，申请不到锁直接返回;
	函数可以定义一个回调函数，当出现数据库忙时sqlite会调用该函数进行延时并返回非0会重试本次操作，回调函数的第二个参数会被传递为此次因BUSY忙事件而调用该函数的次数,因此完全可以自行控制多少次后(也就是延时多少后)才真正返回BUSY;

	回调函数返回非０,数据库会重试当前操作，返回０则当前操作返回SQLITE_BUSY;


int sqlite3_busy_timeout(…)

	参数:  sqlite3 *db,   int ms
	不注册此函数时默认超时等待为0，当ms<=0时，清除busy handle，申请不到锁直接返回;
	定义一个毫秒数，当未到达该毫秒数时，sqlite会sleep并重试当前操作,
	如果超过ms毫秒，仍然申请不到需要的锁，当前操作返回SQLITE_BUSY;
	PS:这个函数实际上注册了一个默认的sqlite3_busy_handler（sqliteDefaultBusyCallback），而这个回调函数在编译环境下可能使得第二个ms参数必需要大于1000且是他的整数倍才有意义,因此此默认callback函数延时较大。



sqlite3_interrupt
	函数主体：
void sqlite3_interrupt(sqlite3 *db){			 db->u1.isInterrupted = 1;
}

	函数说明：在最早的时刻停止任何引起挂起的操作createfun函数
	sqlite3CreateFunc
	sqlite3_create_function
	sqlite3_create_function_v2
	sqlite3_create_function16
    sqlite3CreateFunc和sqlite3_create_function
	相同点：这两个函数都是用来穿件用户函数的
	不同点： sqlite3CreateFunc是一个SQlite内部函数，而sqlite3_create_function提供给客户应用程序调用的API。且sqlite3_create_function在分配内存操作失败时，会返回错误码并清除内存分配错误标示
sqlite_create_function()和sqlite3_create_function16()

	sqlite3_create_function16()和sqlite_create_function()的函数功能是一样的。
	不同之处在于自定义的函数名一个要求是 UTF-16 编码，而另一个则要求是 UTF-8.
sqlite3_create_function_v2()
    版本管理（Version Management）

         const char *sqlite3_libversion(void);
        以UTF-8编码字符串的形式，返回SQLite库的版本。SQLite还定义了一个宏：SQLITE_VERSION，它的值也是代表SQLite库版本的UTF-8编码字符串。

	    以一个整形数的形式，返回SQLite库的版本，格式是MNNNPPP。M是主版本号，对于SQLite3数据库，M的值为3。N是次版本号，P代表发行点。	  宏定义SQLITE_VERSION_NUMBER，与sqlite3_libversion_number函数功能相同。

	    const char *sqlite3_sourceid(void);   返回一个字符串指针，指向的字符串中包含日期、时间戳和一个SHA1哈希值。宏定义SQLITE_SOURCE_ID的功能与该函数相同。
	    程序员可以利用上面的几个函数和对应的宏定义来验证SQLite库的版本。宏定义来自头文件sqlite.h中，而函数调用获取的版本号来自程序所链接的SQLite
	    library中。如：	if(SQLITE_VERSION_NUMBER > sqlite3_libversion_number()) { /* library too old; report error and exit. */ }   在这段程序中，SQLITE_VERSION_NUMBER的值来自我们的客户应用程序在编译时所使用的sqlite.h头文件中。而sqlite3_libversion_number()函数调用所返回的版本号，来自应用程序运行时所连接的library的版本。所以在我们的应用程序中，可以做如上代码的比较，以判断应用程序运行时，所连接的library是否比我们的应用程序编译时所使用的版本低，如果是则发出错误报告或者退出程序。


库初始化：

	int sqlite3_initialize(void);
	int sqlite3_shutdown(void);
	在使用SQlite Library之前，首先应该调用sqlite3_initialize函数，该函数将分配资源，初始化一些必要的数据结构。与之配合使用的另一个函数是sqlite3_shutdown，该函数用来释放由sqlite3_initialize分配的资源。
	    不过在很多客户的应用程序中通常直接调用sqlite3_open或者另一些主要的API函数，这些函数会自动初始化SQlite library（如果它还没有被初始化的话）。不过还是推荐客户应用程序显示调用这两个APIs函数来完成SQlite library的初始化和最后的清理工作。	                           在执行任何SQL语句之前，必须首先连接到一个数据库，也就是打开或者新建一个SQlite3数据库文件。连接数据库由sqlite3_open函数完成，它一共有上面3个版本。其中 sqlite3_open函数假定SQlite3数据库文件名为UTF-8编码，sqlite3_open_v2是它的加强版。sqlite3_open16函数假定SQlite3数据库文件名为UTF-16（Unicode宽字符）编码。

	    所有这三个函数，参数filename是要连接的SQlite3数据库文件名字符串。参数ppDb看起来有点复杂，它是一个指向指针的指针。当调用sqlite3_open_xxx函数时，该函数将分配一个新的SQlite3数据结构，然后初始化，然后将指针ppDb指向它。所以客户应用程序可以通过sqlite3_open_xxx函数连接到名为filename的数据库，并通过参数ppDb返回指向该数据库数据结构的指针。


释放分配的资源

	sqlite3_exec
	int sqlite3_exec(此接口是一个对sqlite3_prepare_v2, sqlite3_step,sqlite3_finalize接口的便捷封装，以便应用程序可以用少量的代码来运行多条SQL语句。
        参数解释：第1个为数据库连接对象，通过sqlite3_open得到；第2个为要实行的SQL语句，使用UTF-8编码，多条语句使用分号间隔；第3个为回调函数，对结果进行处理，第4个为传递给callback函数的第一个参数；第5个参数，当该接口执行错误的时候，可以得到错误信息。
如果sqlite3_exec执行错误，errmsg有错误返回的时候，在调用sqlite3_close()之前，必须调用sqlite3_free来释放掉sqlite3_malloc()的内存。
	sqlite3_close
	int sqlite3_close(sqlite3 *);
	该接口销毁之前调用sqlite3_open_v2()创建的数据库连接对象。所有与该连接相关的预处理语句对象都必须在关闭连接之前销毁，否则该接口会返回SQLITE_BUSY。

	sqlite3_reset
	int sqlite3_reset(sqlite3_stmt*);
	该接口用来重置一个SQL预处理语句对象的状态，使得它可以被再次执行。

	SQLite的内存数据结构
	       SQLite通过动态内存分配来获取各种对象（例如数据库连接和SQL预处理语句）所需内存、建立数据库文件的内存Cache、以及保存查询结果。
	SQLite将页内存分配系统实现为两层（如下图3.1）：
	1、PCache结构为页内存分配系统的上层接口结构，与具体的实现无关。
	用户定义缓冲区模块，PCache1结构为页内存分配系统的默认实现，直接对页空间的分配与回收，上层使用者无需关心其内部实现。
