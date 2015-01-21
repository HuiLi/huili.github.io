# Mutex初始化

大量使用sqliteInt.h定义的结构体

	int sqlite3MutexInit(void){
	  int rc = SQLITE_OK;
	  if( !sqlite3GlobalConfig.mutex.xMutexAlloc ){
	    /* If the xMutexAlloc method has not been set, then the user did not
	    ** install a mutex implementation via sqlite3_config() prior to
	    ** sqlite3_initialize() being called. This block copies pointers to
	    ** the default implementation into the sqlite3GlobalConfig structure.
	    */
	//    ** the default implementation into the sqlite3GlobalConfig structure.
	//    **如果xMutexAlloc 函数没设置，
	//    ** 这段复制指针向sqlite3GlobalConfig默认实现结构。
	//     */
	/************************************************************************/
	 //这段内容讲sqliteInt.h" 中sqlite3_mutex_methods结构体中 mutex 的xMutexAlloc无定义，则从sqlite3_mutex_methods 复制一个
	  /************************************************************************/
	  //majin78 xMutexAlloc未设置，定义于"sqliteInt.h" 前提是系统不支持可写静态数据
	  //内容为 #define sqlite3GlobalConfig GLOBAL(struct Sqlite3Config, sqlite3Config) 宏定义为Sqlite3Config结构体，后一参数为结构体size
	  //majin78 sqlite3GlobalConfig.mutex 属性为sqlite3_mem_methods 定义于sqlite3.h  内容为 typedef struct sqlite3_mutex_methods sqlite3_mutex_methods;
	  //struct sqlite3_mutex_methods 定义于sqlite3.h 为一些函数指针
	sqlite3_mutex_methods const *pFrom;
	    sqlite3_mutex_methods *pTo = &sqlite3GlobalConfig.mutex;

	    if( sqlite3GlobalConfig.bCoreMutex ){// 定义于"sqliteInt.h" 内容为 int bCoreMutex;                   /* True to enable core mutexing */
	      pFrom = sqlite3DefaultMutex();//定义于"sqliteInt.h"   sqlite3_mutex_methods const *类型  需先定义SQLITE_MUTEX_OMIT
	    }else{
	      pFrom = sqlite3NoopMutex();// 定义于"sqliteInt.h"   sqlite3_mutex_methods const *类型  需先定义SQLITE_MUTEX_OMIT
	    }
	    memcpy(pTo, pFrom, offsetof(sqlite3_mutex_methods, xMutexAlloc));//offsetof该宏用于求结构体中一个成员在该结构体中的偏移量。
	    memcpy(&pTo->xMutexFree, &pFrom->xMutexFree,
	           sizeof(*pTo) - offsetof(sqlite3_mutex_methods, xMutexFree));
	    pTo->xMutexAlloc = pFrom->xMutexAlloc;
	  }
	  rc = sqlite3GlobalConfig.mutex.xMutexInit();

	#ifdef SQLITE_DEBUG//调试时支持支持可写静态数据
	  GLOBAL(int, mutexIsInit) = 1;//mutexIsInit赋值1
	#endif
	  return rc;
	}

