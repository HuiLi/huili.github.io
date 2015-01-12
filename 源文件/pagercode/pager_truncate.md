# pager_truncate
    static int pager_truncate(Pager *pPager, Pgno nPage){
      int rc = SQLITE_OK;
      assert( pPager->eState!=PAGER_ERROR );
      assert( pPager->eState!=PAGER_READER );

      if( isOpen(pPager->fd)
       && (pPager->eState>=PAGER_WRITER_DBMOD || pPager->eState==PAGER_OPEN)
      ){
        i64 currentSize, newSize;
        int szPage = pPager->pageSize; assert( pPager->eLock==EXCLUSIVE_LOCK );
    /* TODO：它是安全的在这里使用Pager.dbFileSize？*/
    rc = sqlite3OsFileSize(pPager->fd, &currentSize);
    newSize = szPage*(i64)nPage;
    if( rc==SQLITE_OK && currentSize!=newSize ){
      if( currentSize>newSize ){
        rc = sqlite3OsTruncate(pPager->fd, newSize);
      }else if( (currentSize+szPage)<=newSize ){
        char *pTmp = pPager->pTmpSpace;
        memset(pTmp, 0, szPage);
        testcase( (newSize-szPage) == currentSize );
        testcase( (newSize-szPage) >  currentSize );
        rc = sqlite3OsWrite(pPager->fd, pTmp, szPage, newSize-szPage);
      }
      if( rc==SQLITE_OK ){
        pPager->dbFileSize = nPage;
      }
        }
      }
      return rc;
    }

这个函数用来改变文件系统中数据库文件的真正大小。这仅仅发生在提交一个事务或回滚一个事务（包括回滚一个热日志）。
如果主数据库文件没有打开，或者pager不是在DBMOD或者 OPEN状态，这个函数是一个空操作。否则，文件的大小改
变为nPage。
如果当前磁盘上的文件大于nPage页面,则使用VFSxTruncate()方法截断它。
如果成功则返回SQLITE_OK，如果当修改数据库文件时发生IO错误，返回错误代码给调用者。或者也许可能磁盘上的
文件小于nPage页面。一些操作系统启用可能出现困惑，如果你想截取一个文件，所以有这种情况，写一个单独的0字节在新文件的结束。

