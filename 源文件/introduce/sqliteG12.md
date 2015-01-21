# Sqlite源代码介绍
sqlite-src-3071401，版本为3.7.14.1，代码有四种模式，分别在不同状态下使用
SQLITE_MUTEX_OMIT 不适用互斥体的代码，初始化时，静态互斥体结构体不被覆盖。
SQLITE_MUTEX_NOOP 单线程应用时代码，没有提供互斥排它锁，初始化时，静态互斥体结构体被覆盖。
SQLITE_MUTEX_PTHREADS UNIX 多线程调用的代码
SQLITE_MUTEX_W32 WIN32下多线程调用的代码

	/*
	** Figure out what version of the code to use.  The choices are
	**
	**   SQLITE_MUTEX_OMIT         No mutex logic.  Not even stubs.  The
	**                             mutexes implemention cannot be overridden
	**                             at start-time.//初始化时不被重置
	**
	**   SQLITE_MUTEX_NOOP         For single-threaded applications.  No
	**                             mutual exclusion is provided.  But this
	**                             implementation can be overridden at
	**                             start-time.//只使用单进程调用
	**
	**   SQLITE_MUTEX_PTHREADS     For multi-threaded applications on Unix.//Unix调用
	**
	**   SQLITE_MUTEX_W32          For multi-threaded applications on Win32.//Win32调用
	*/
