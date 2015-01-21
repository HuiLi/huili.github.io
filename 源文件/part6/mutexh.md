# mutex.h

申明了一系列的函数，需声明SQLITE_MUTEX_OMIT，表示只使用申明函数，而不具体实现，因为SQLITE_MUTEX_OMIT这种状态不使用互斥体，故只需申明即可。文件主要包含互斥体使用函数申明，具体的实现在mutex.c里面。

	#ifdef SQLITE_MUTEX_OMIT
	/*
	** If this is a no-op implementation, implement everything as macros.
	//宏定义了一系列的函数，需声明SQLITE_MUTEX_OMIT
	*/
	#define sqlite3_mutex_alloc(X)    ((sqlite3_mutex*)8)
	#define sqlite3_mutex_free(X)
	#define sqlite3_mutex_enter(X)
	#define sqlite3_mutex_try(X)      SQLITE_OK
	#define sqlite3_mutex_leave(X)
	#define sqlite3_mutex_held(X)     ((void)(X),1)
	#define sqlite3_mutex_notheld(X)  ((void)(X),1)
	#define sqlite3MutexAlloc(X)      ((sqlite3_mutex*)8)
	#define sqlite3MutexInit()        SQLITE_OK
	#define sqlite3MutexEnd()
	#define MUTEX_LOGIC(X)
	#else
	#define MUTEX_LOGIC(X)            X  //定义了SQLITE_DEBUG 才使用，防止调试时出错
	#endif /* defined(SQLITE_MUTEX_OMIT) */

