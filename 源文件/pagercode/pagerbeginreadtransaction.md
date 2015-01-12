# pagerBeginReadTransaction
    static int pagerBeginReadTransaction(Pager *pPager){
      int rc;                         /* Return code */
      int changed = 0;                /* True if cache must be reset */

      assert( pagerUseWal(pPager) );
      assert( pPager->eState==PAGER_OPEN || pPager->eState==PAGER_READER );

    sqlite3WalEndReadTransaction(pPager->pWal);

      rc = sqlite3WalBeginReadTransaction(pPager->pWal, &changed);
      if( rc!=SQLITE_OK || changed ){
        pager_reset(pPager);
      }

      return rc;
    }
开始读事务。这个路径常常被称为“pagerOpenSnapshot()”,因为它本质上是在当前时间点数据库发热快照和保存快照,供读
者使用,不管其他进程或checkpointers同时变化。
