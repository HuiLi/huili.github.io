# API介绍

初始化分配内存子系统

 int sqlite3MallocInit(void){

  if( sqlite3GlobalConfig.m.xMalloc==0 ){/*没有分配*/

    sqlite3MemSetDefault();
  }

  memset(&mem0, 0, sizeof(mem0));/*分配内存*/

  if( sqlite3GlobalConfig.bCoreMutex ){/*核心锁*/

    mem0.mutex = sqlite3MutexAlloc(SQLITE_MUTEX_STATIC_MEM);/*分配一个互斥锁*/
  }

  if( sqlite3GlobalConfig.pScratch && sqlite3GlobalConfig.szScratch>=100/*szScratch每个缓冲区的大小*/
      && sqlite3GlobalConfig.nScratch>0 )

      {/*nScratch缓冲区的数量*/
    int i, n, sz;
    ScratchFreeslot *pSlot;/*空闲缓冲区的指针*/
    sz = ROUNDDOWN8(sqlite3GlobalConfig.szScratch);/*四舍五入*/
    sqlite3GlobalConfig.szScratch = sz;/*重新获得的值给szScratch*/
    pSlot = (ScratchFreeslot*)sqlite3GlobalConfig.pScratch;/*将sqlite3GlobalConfig头指针给pSlot*/
    n = sqlite3GlobalConfig.nScratch;/*缓冲区数量给变量n*/
    mem0.pScratchFree = pSlot;/*pSlot给mem0的空闲缓冲区地址*/
    mem0.nScratchFree = n;/*缓冲光的个数nScratchFree是n*/
    for(i=0; i<n-1; i++){/*求出最后一个缓冲区的地址*/
      pSlot->pNext = (ScratchFreeslot*)(sz+(char*)pSlot);
      pSlot = pSlot->pNext;
    }

    pSlot->pNext = 0;
    mem0.pScratchEnd = (void*)&pSlot[1];/*mem0的最后地址pSlot*/
  }

  else

    {/*空间不够全部清空为0*/
    mem0.pScratchEnd = 0;
    sqlite3GlobalConfig.pScratch = 0;
    sqlite3GlobalConfig.szScratch = 0;
    sqlite3GlobalConfig.nScratch = 0;
    }

  if( sqlite3GlobalConfig.pPage==0 || sqlite3GlobalConfig.szPage<512
      || sqlite3GlobalConfig.nPage<1 )

      {/*pPage页缓存没有分配地址 或者地址小于1 nPage页数小于一页 */
    sqlite3GlobalConfig.pPage = 0;
    sqlite3GlobalConfig.szPage = 0;
    sqlite3GlobalConfig.nPage = 0;
  }
  return sqlite3GlobalConfig.m.xInit(sqlite3GlobalConfig.m.pAppData);
}

sqlite3_soft_heap_limit().
**如果堆内存没有满  返回真

int sqlite3HeapNearlyFull(void)
{
  return mem0.nearlyFull;/*内存是否满*/
}



分配内存空间的函数：

    void *sqlite3Realloc(void *pOld, int nBytes)
    void *sqlite3_realloc(void *pOld, int n)
    void *sqlite3DbRealloc(sqlite3 *db, void *p, int n)
    void *sqlite3DbMallocZero(sqlite3 *db, int n)
    void *sqlite3DbMallocRaw(sqlite3 *db, int n)
    int sqlite3DbMallocSize(sqlite3 *db, void *p)

得到分配内存大小

    void *sqlite3DbMallocZero(sqlite3 *db, int n)
    void *sqlite3DbMallocRaw(sqlite3 *db, int n)

具体实例：

    分配内存就是使用内存和释放内存，该例程是类似于了alloc()，因为目的不是为了内存被长期执行例子是为了用了很久的内存，大内存，临时内存的数据结构，该数据结构不能满足嵌入式处理器的堆栈

    void *sqlite3ScratchMalloc(int n)
    void sqlite3ScratchFree(void *p){/*释放缓冲区*/
    释放内存的函数
    sqlite3_release_memory(allocSize);//释放内存
    sqlite3ScratchFree(void *p){/*释放缓冲区*///释放从sqlite3Malloc函数中得到的内存
void sqlite3_free(void *p)



