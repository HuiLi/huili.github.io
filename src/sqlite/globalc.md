# Global.c模块源码分析
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Global.c文件定义全局变量和常量，将一个字符串全部由大写转换为对应的小写，自从表的数目及其本身大小远远大于SQLite数据库本身，我们不使用UTF格式编码，我们只是用US-ASCII 或者 EBCDIC（二进制编码的十进制交换码）编码方式中的一种。该文件有一个数据结构sqlite3CtypeMap[256]用来存储各种字符。并且用以下结构体存储数据库有关的全局变量设置。

    SQLITE_WSD struct Sqlite3Config sqlite3Config = {
       SQLITE_DEFAULT_MEMSTATUS,  /* bMemstat */
       1,                         /* bCoreMutex */
       SQLITE_THREADSAFE==1,      /* bFullMutex */以下的设置只要一个数据库连接不被多个线程同时使用就是安全的
       SQLITE_USE_URI,            /* bOpenUri */
       0x7ffffffe,                /* mxStrlen 最大长度*/
       128,                       /* szLookaside */
       500,                       /* nLookaside */
       {0,0,0,0,0,0,0,0},         /* m */
       {0,0,0,0,0,0,0,0,0},       /* mutex */
       {0,0,0,0,0,0,0,0,0,0,0,0,0},/* pcache2 */
       (void*)0,                  /* pHeap */
       0,                         /* nHeap */
       0, 0,                      /* mnHeap, mxHeap */
       (void*)0,                  /* pScratch */
       0,                         /* szScratch */
       0,                         /* nScratch */
       (void*)0,                  /* pPage */
       0,                         /* szPage */
       0,                         /* nPage */
       0,                         /* mxParserStack */
       0,                         /* sharedCacheEnabled */
       /* All the rest should always be initialized to zero */
       0,                         /* isInit */
       0,                         /* inProgress */
       0,                         /* isMutexInit */
       0,                         /* isMallocInit */
       0,                         /* isPCacheInit */
       0,                         /* pInitMutex */
       0,                         /* nRefInitMutex */
       0,                         /* xLog */
       0,                         /* pLogArg */
       0,                         /* bLocaltimeFault */
    };




<img src="transform.png">
