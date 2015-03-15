# 互斥体enter使用

例程企图进入一个互斥锁。如果另一个线程已经包含了这个互斥锁，sqlite3_mutex_enter()将被阻塞。被创建的互斥锁使用SQLITE_MUTEX_RECURSIVE可以被相同的线程多次键入。在这样的情况下，在另一个线程进入之前，互斥锁必须退出相同的次数。

	void sqlite3_mutex_enter(sqlite3_mutex *p){
	  if( p ){
	    sqlite3GlobalConfig.mutex.xMutexEnter(p);//  void (*xMutexEnter)(sqlite3_mutex *);
	  }
	}

