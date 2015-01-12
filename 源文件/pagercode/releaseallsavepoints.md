# releaseAllSavepoints
    static void releaseAllSavepoints(Pager *pPager){
      int ii;
      for(ii=0; ii<pPager->nSavepoint; ii++){
        sqlite3BitvecDestroy(pPager->aSavepoint[ii].pInSavepoint);
      }
      if( !pPager->exclusiveMode || sqlite3IsMemJournal(pPager->sjfd) ){
        sqlite3OsClose(pPager->sjfd);
      }
      sqlite3_free(pPager->aSavepoint);
      pPager->aSavepoint = 0;
      pPager->nSavepoint = 0;
      pPager->nSubRec = 0;
    }
释放页面里的所有结构体。aSavepoint[]数组和集合并Pager.aSavepoint 和 Pager.nSavepoint清零。
如果sub-journal打开或者页面没有加上排它锁关闭sub-journal。

