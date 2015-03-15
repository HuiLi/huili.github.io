# 互斥体try使用

例程企图进入一个互斥锁，sqlite3_mutex_enter()不被阻塞。互斥锁一旦成功键入，sqlite3_mutex_try()接口将返回一个标志位，这个标志位是 SQLITE_OK。

	int sqlite3_mutex_try(sqlite3_mutex *p){
	  int rc = SQLITE_OK;
	  if( p ){
	    return sqlite3GlobalConfig.mutex.xMutexTry(p);//  int (*xMutexTry)(sqlite3_mutex *);
	  }
	  return rc;
	}

