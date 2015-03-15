# mutex.c

具体实现函数，非SQLITE_MUTEX_OMIT状态，下面是具体函数功能

|编号|函数名|功能
| -- | -- | -- |
|1|int sqlite3MutexInit(void)| 初始化互斥体变量
|2|int sqlite3MutexEnd(void)|关闭互斥系统。释放sqlite3MutexInit()分配的资源。调用结构体中xMutexEnd，返回RC
|3|sqlite3_mutex *sqlite3_mutex_alloc(int id)|获取一个指向静态互斥锁或分配一个新的动态互斥锁。根据类型（id）分配新互斥锁
|4|sqlite3_mutex *sqlite3MutexAlloc(int id)|功能同上
|5|void sqlite3_mutex_free(sqlite3_mutex *p)|释放一个动态互斥锁
|6|void sqlite3_mutex_enter(sqlite3_mutex *p)|获得互斥锁p。如果其他线程已经拥有互斥锁,那么就将其阻塞,直到它可以获得。
|7|int sqlite3_mutex_try(sqlite3_mutex *p)|获得互斥锁p。如果成功,返回SQLITE_OK。否则,如果另一个线程持有互斥锁,它不能得到,就返回SQLITE_BUSY。
|8|void sqlite3_mutex_leave(sqlite3_mutex *p)|sqlite3_mutex_leave() 程序退出之前由相同的线程输入的互斥对象。如果一个空指针作为参数传递给该函数，那么就不做任何操作
|9|int sqlite3_mutex_held(sqlite3_mutex *p)|调试状态P为0返回 xMutexHeld分配失败返回0 否则返回1
|10|int sqlite3_mutex_notheld(sqlite3_mutex *p)|P为0返回 xMutexHeld分配失败返回0， 否则返回1

