# Mutex_w32.c

用于支持win32系统下的多进程，源代码必须宏定义SQLITE_MUTEX_W32才能编译该段代码，mutex_w32.c包含Sqlite在win32系统下互斥体具体实现代码，其实主要是使用win32系统下面的临界区接口。

下图是包含函数名，以及相应功能，顺序从上往下

|编号|函数名|功能
| -- | -- | -- |
|1|static int mutexIsNT(void)| Win95/98/ME 不支持LockFileEx()mutexIsNT ()用于TryEnterCriticalSection() 的调用
|2|static int winMutexHeld(sqlite3_mutex *p)|SQLITE_DEBUG模式下，用于assert()里面使用，主要调试查看变量，防止出BUG，前文说过使用互斥量非常谨慎，如此可见一斑
|3|static int winMutexNotheld2(sqlite3_mutex *p, DWORD tid)|SQLITE_DEBUG模式下，用于assert()里面使用，主要调试查看变量，防止出BUG，前文说过使用互斥量非常谨慎，如此可见一斑
|4|sstatic int winMutexNotheld(sqlite3_mutex *p)|SQLITE_DEBUG模式下，用于assert()里面使用，主要调试查看变量，防止出BUG，前文说过使用互斥量非常谨慎，如此可见一斑
|5|static sqlite3_mutex winMutex_staticMutexes[6]|初始与取消初始设置
|6|static int winMutex_isInit|静态函数定义
|7|static long winMutex_lock|
|8|static int winMutexInit(void)|初始化互斥体变量
|9|static int winMutexEnd(void)|删除互斥体变量
|10|static sqlite3_mutex *winMutexAlloc(int iType)|作用是分配6种之一互斥体，并返回指针，如果分配失败，返回相应错误
|11|static void winMutexFree(sqlite3_mutex *p)|释放互斥体变量
|12|static void winMutexEnter(sqlite3_mutex *p)|请求互斥锁，sqlite3_mutex_enter()导致阻塞
|13|static int winMutexTry(sqlite3_mutex *p)|请求互斥锁，sqlite3_mutex_try()返回SQLITE_BUSY
|14|static void winMutexLeave(sqlite3_mutex *p)|解锁互斥锁离开临界区
|15|sqlite3_mutex_methods const *sqlite3DefaultMutex(void)|设置默认互斥体变量


