# 锁机制在Windows中的实现源码
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在os_win.c中：

    /*
    为文件加锁。
    加锁的类型locktype，为以下之一：
    (1) SHARED_LOCK
    (2) RESERVED_LOCK
    (3) EXCLUSIVE_LOCK
    加锁类型不能为PENDING_LOCK，PENDING_LOCK为内部自    动过渡的一种锁，外部用户不应显示的加此种锁。代    码也做了判断：assert( locktype!=PENDING_LOCK );
    */
    static int winLock(sqlite3_file *id, int     locktype){
    int rc = SQLITE_OK; /* 返回值 */
    int res = 1; /* Windows锁函数操作的返回值*/
    int newLocktype;     /*在退出前将pFile->locktype设为此值*/
    int gotPendingLock = 0;/*     标识此次是否申请了一个PENDING lock this time */
    winFile *pFile = (winFile*)id;
    DWORD lastErrno = NO_ERROR;
    assert( id!=0 );
    OSTRACE(("LOCK %d %d was %d(%d)\n",
    pFile->h, locktype, pFile->locktype,     pFile->sharedLockByte));
    /* 如果申请的锁级别没有当前文件所拥有的高，则直    接返回*/
    if( pFile->locktype>=locktype ){
    return SQLITE_OK;
    }
    /* 以下三行代码判断当前所申请的锁类型是否合法 */
    /*
    1、如果当前数据库的锁类型为NO_LOCK，则所申请的锁    类型要为SHARED_LOCK
    2、所申请的锁类型不能为PENDING_LOCK，因为其为一    种过渡锁
    3、如果所申请的锁类型为RESERVED     _LOCK，则当前数据库的锁类型要为SHARED_LOCK
    */
    assert( pFile->locktype!=NO_LOCK ||     locktype==SHARED_LOCK );
    assert( locktype!=PENDING_LOCK );
    assert( locktype!=RESERVED_LOCK ||     pFile->locktype==SHARED_LOCK );
    newLocktype = pFile->locktype;
    /*
    如果我们申请 PENDING_LOCK 或     SHARED_LOCK，则需要对加锁区域 PENDING_BYTE 进行加锁    ；如果申请的是 SHARED_LOCK ，则锁 PENDING_BYTE 区域只是暂时的。
    1、如果当前数据库处于无锁状态，则首先要获取共享锁，这也是读事务和写事务在最初阶段都要经历的阶段
    2、如果前数据库处于保留锁状态，而申请排它锁进行写库，则要先获取未决锁。
    此种情况是为了阻止其它进程对此库继续申请共享锁，以防止写饿死。
    */
    if(   (pFile->locktype==NO_LOCK)
    || (   (locktype==EXCLUSIVE_LOCK)
    && (pFile->locktype==RESERVED_LOCK))
    ){
    int cnt = 3;
    /* 获取未决锁 */
    while( cnt-->0 && (res = winLockFile(&pFile->h,     SQLITE_LOCKFILE_FLAGS,
    PENDING_BYTE, 0, 1, 0))==0 ){
    /* 尝试3次获得挂起锁。这是必要的工作**可能是win    dows系统中索引和/或防病毒软件的问题造成的。
    */
    OSTRACE(("could not get a PENDING lock. cnt=%d\n", cnt));
    if( cnt ) sqlite3_win32_sleep(1);
    }
    /* 设置gotPendingLock为1，后面的程序根据此值可能会释放PENDING锁*/
    gotPendingLock = res;
    if( !res ){
    lastErrno = osGetLastError();
    }
    }
    /*
    获取 SHARED_LOCK
    此时,事务应该持有 PENDING_LOCK（gotPendingLock == 1）。PENDING_LOCK 作为事务从 NO_LOCK 到 SHARED_LOCK 的一个过渡，实际上此时锁处于两个状态:PENDING和SHARED，直到后面释放 PENDING_LOCK 后，才真正处于SHARED状态。
    */
    if( locktype==SHARED_LOCK && res ){
    assert( pFile->locktype==NO_LOCK );
    res = getReadLock(pFile);
    if( res ){
    newLocktype = SHARED_LOCK;
    }else{
    lastErrno = osGetLastError();
    }
    }
    /*
    获取 RESERVED_LOCK
    此时事务应持有     SHARED_LOCK，变化过程为SHARED->RESERVED。
    RESERVED锁的作用就是为了提高系统的并发性能。
    */
    if( locktype==RESERVED_LOCK && res ){
    assert( pFile->locktype==SHARED_LOCK );
    res = winLockFile(&pFile->h, SQLITE_LOCKFILE_FLAGS, RESERVED_BYTE, 0, 1, 0);
    if( res ){
    newLocktype = RESERVED_LOCK;
    }else{
    lastErrno = osGetLastError();
    }
    }
    /*
    获取 PENDING_LOCK
    此时事务持有 RESERVED_LOCK，且事务申请EXCLUSIVE_LOCK。
    变化过程为:RESERVED->PENDING。
    PENDING状态唯一的作用就是防止写饿死。
    读事务不会执行此段代码，但写事务会执行该代码。
    执行该代码后gotPendingLock设为0，后面就不会释放 RESERVED_LOCK 了。
    */
    if( locktype==EXCLUSIVE_LOCK && res ){
    /*
    这里没有实际的加锁操作,因为PENDING锁前面已经加过了，
    只是把锁的状态改为PENDING状态
    */
    newLocktype = PENDING_LOCK;
    /*
    设置gotPendingLock,后面就不会释放PENDING锁了,
    相当于加了PENDING锁,实际上是在开始处加的PENDING锁
    */
    gotPendingLock = 0;
    }
    /*
    获取EXCLUSIVE_LOCK
    此时事务应该持有 PENDING_LOCK，事务准备写库。
    变化过程:PENDING->EXCLUSIVE
    */
    if( locktype==EXCLUSIVE_LOCK && res ){
    assert( pFile->locktype>=SHARED_LOCK );
    /* 先解除该进程对数据库加的共享锁 */
    res = unlockReadLock(pFile);
    OSTRACE(("unreadlock = %d\n", res));
    /*
    对SHARED_FIRST区域加排它锁，防止其它进程读库。
    1、如果加锁成功，则说明没有其它进程在读库，因此可进行写库操作
    2、如果加锁失败，则说明仍有其它进程正在进行读操作，此时则无法获取EXCLUSIVE_LOCK，因此也无法写库，事务仍持有PENDING_LOCK
    */
    res = winLockFile(&pFile->h, SQLITE_LOCKFILE_FLAGS, SHARED_FIRST, 0,
    SHARED_SIZE, 0);
    if( res ){
    newLocktype = EXCLUSIVE_LOCK;
    }else{
    lastErrno = osGetLastError();
    OSTRACE(("error-code = %d\n", lastErrno));
    /* 获取排它锁失败，则继续对文件加共享锁 */
    getReadLock(pFile);
    }
    }
    /*
    如果申请的是 SHARED_LOCK，此时需要释放在前面获得的PENDING_LOCK。
    锁的变化为:PENDING->SHARED
    */
    if( gotPendingLock && locktype==SHARED_LOCK ){
    winUnlockFile(&pFile->h, PENDING_BYTE, 0, 1, 0);
    }
    /* 改变文件的锁状态，返回适当的结果码 */
    if( res ){
    rc = SQLITE_OK;
    }else{
    OSTRACE(("LOCK FAILED %d trying for %d but got     %d\n", pFile->h,
    locktype, newLocktype));
    pFile->lastErrno = lastErrno;
    rc = SQLITE_BUSY;
    }
    pFile->locktype = (u8)newLocktype;
    return rc;
    }
    读锁实现代码如下：
    static int getReadLock(winFile *pFile){
    int res;
    /*判断操作系统类型*/
    if( isNT() ){
    /*为WinNT/2K/XP系统，则直接使用LockFileEx对SHARED_SIZE进行加共享锁*/
    #if SQLITE_OS_WINCE
    /*
    ** NOTE: Windows CE is handled differently here due its lack of the Win32
    **       API LockFileEx.
    */
    res = winceLockFile(&pFile->h, SHARED_FIRST, 0, 1, 0);
    #else
    res = winLockFile(&pFile->h,     SQLITE_LOCKFILEEX_FLAGS, SHARED_FIRST, 0,
    SHARED_SIZE, 0);
    #endif
    }
    #ifdef SQLITE_WIN32_HAS_ANSI
    else{
    /*
    为Win95, Win98, and     WinME系统，则使用LockFile对SHARED_SIZE中的随机字节进行加锁（此锁不能重叠）
    */
    int lk;
    sqlite3_randomness(sizeof(lk), &lk);
    pFile->sharedLockByte = (short)((lk &     0x7fffffff)%(SHARED_SIZE - 1));
    res = winLockFile(&pFile->h,     SQLITE_LOCKFILE_FLAGS,
    SHARED_FIRST+pFile->sharedLockByte, 0, 1, 0);
    }
    #endif
    if( res == 0 ){
    pFile->lastErrno = osGetLastError();
    /* No need to log a failure to lock */
    }
    return res;
    }
    static BOOL winLockFile(
    LPHANDLE phFile,
    DWORD flags,
    DWORD offsetLow,
    DWORD offsetHigh,
    DWORD numBytesLow,
    DWORD numBytesHigh
    ){
    #if SQLITE_OS_WINCE
    return winceLockFile(phFile, offsetLow, offsetHigh, numBytesLow, numBytesHigh);
    #else
    if( isNT() ){
    OVERLAPPED ovlp;
    memset(&ovlp, 0, sizeof(OVERLAPPED));
    ovlp.Offset = offsetLow;
    ovlp.OffsetHigh = offsetHigh;
    return osLockFileEx(*phFile, flags, 0,     numBytesLow, numBytesHigh, &ovlp);
    }else{
    return osLockFile(*phFile, offsetLow, offsetHigh, numBytesLow,
    numBytesHigh);
    }
    #endif
    }
