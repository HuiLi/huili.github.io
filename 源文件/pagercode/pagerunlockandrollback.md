# pagerUnlockAndRollback
    static void pagerUnlockAndRollback(Pager *pPager){
      if( pPager->eState!=PAGER_ERROR && pPager->eState!=PAGER_OPEN ){
        assert( assert_pager_state(pPager) );
        if( pPager->eState>=PAGER_WRITER_LOCKED ){
          sqlite3BeginBenignMalloc();
          sqlite3PagerRollback(pPager);
          sqlite3EndBenignMalloc();
        }else if( !pPager->exclusiveMode ){
          assert( pPager->eState==PAGER_READER );
          pager_end_transaction(pPager, 0);
        }
      }
      pager_unlock(pPager);
    }
如果一个事务是活跃的而且数据库文件是打开的，执行一个回滚。如果pager已经是异常状态，不要在这个时候企图回滚，
而是调用pager_unlock()。调用pager_unlock()将会丢弃所有在内存中的页面，
解锁数据库文件，pager转换回打开状态。若果这意味着有一
个hot-journal被留在文件系统，下一个获得一个共享锁的链
接将会回滚。如果pager还没有进入异常状态，但是在回滚时
发生malloc错误或IO错误，它自己就会进入异常状态，像上面描述的一样，在调用pager_unlock()它将会被清除。
