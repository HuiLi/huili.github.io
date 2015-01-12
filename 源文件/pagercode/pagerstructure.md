# Pager的数据结构
    struct Pager{sqlite3_vfs*pVfs;/*使用IO操作系统功能*/
    u8 exclusiveMode;/*如果锁定模式=排斥则布尔值为真*/
    u8 journalMode;/*一个日志模式的值*/
    u8 useJournal;/*使用一个滚回日志文件*/
    u8 noSync;/*如果为真则不同步*/
    u8 fullSync;/*做额外的同步健壮性的杂志吗*/
    u8 ckptSyncFlags;/*同步正常或完全同步检查点*/
    u8 walSyncFlags;/*同步正常或同步完整wal写到*/
    u8 syncFlags;/*同步正常或完全同步*/
    u8 tempFile;/*zFilename是一个临时文件*/
    u8 readOnly;/*读数据库*/
    u8 memDb;/*抑制所有文件I/O*/
    u8 eState;/*寻呼机状态(开放、读者、作家锁定..*/     u8 eLock;/*当前锁在数据库文件*/
    u8 changeCountDone;/*递增change-counter后设置*/     u8 setMaster;/*真的如果m-j名字被写入jrnl*/
    u8 doNotSpill;/*当非零不泄漏缓存*/
    u8 doNotSyncSpill;/*不做一个泄漏,需要jrnl同步*/     u8 subjInMemory;/*真正的使用内存sub-journals*/
    Pgno dbSize;/*在数据库的页面数量*/
    Pgno dbOrigSize;/*dbSize当前事务*/
    Pgno dbFileSize;/*数据库文件的页面数量*/
    Pgno dbHintSize;/*它值传递给FCNTL大小提示电话*/     int errCode;/*几种错误之一*/
    int nRec;/*自去年j-header写页杂志*/
    u32 cksumInit;/*拟随机值添加到每一个校验和*/
    u32 nSubRec;/*数量的记录写入sub-journal*/
    Bitvec*pInJournal;/*为数据库中的每一页文件一点*/
    sqlite3_file *fd;/*文件描述符为数据库*/
    sqlite3_file*jfd;/*文件描述符的主要日志*/
    sqlite3_file*sjfd;/*文件描述符sub-journal*/
    i64journalOff;/*当前日志文件写抵消*/
    i64journalHdr;/*字节抵消之前的日志标题*/
    sqlite3_backup*pBackup;/*指向列表的备份过程*/
    PagerSavepoint*aSavepoint;/*一系列积极的保存点*/
    intnSavepoint;/*aSavepoint[]中元素数辆*/
    chardbFileVers[16];/*更改数据库文件时更改*/
    void (*xReiniter)(DbPage*); /*重新加载页面时调用这个例程 */
    #ifdef SQLITE_HAS_CODEC
    void *(*xCodec)(void*,void*,Pgno,int); /* 常规en /解码数据 */
    void (*xCodecSizeChng)(void*,int,int); /* 页面大小变化的通知 */
    void (*xCodecFree)(void*);             /* 析构函数的编解码器 */
    void *pCodec;               /* 第一个参数xCodec……方法 */
    #endif
    char *pTmpSpace;            /* 寻呼机。页大小为tmp使用字节的空间 */
    PCache *pPCache;            /* 页面缓存对象的指针 */
    #ifndef SQLITE_OMIT_WAL
    Wal *pWal;  /*写前日志使用的“journal_mode=wal” */
    char *zWal; /* 写前日志文件名 */
    #endif
    };

重要的成员变量：

| 变量名称 | 备注 |
| -- | -- |
|eState | pager对象的当前状态 |
| eLock | 当前施加在数据库文件上的锁 |
| changeCountCone | 确认change-counter是否被经常更新 |
| setMaster | 控制主日志文件 |
| doNotSpill,doNotSyncSpill | 控制cache-spills的行为 |
| subjInMemory | 控制内存中的子日志文件 |
| dbSize,dbOrigSize,dbFileSize | dbSize被设置为页码|
|dbHintSize | VFS xFileControl(FCNTL_SIZE_HINT)调用的数量 |
| errCode | 用于PAGER_ERROR状态 |
| JOURNAL_PG_SZ() | 日志中每个页记录大小 |
|assert_pager_state() | 发现pager对象内部状态的不一致性 |
| readMasterJournal() | 读取主日志文件 |
| writeMasterJournal() | 写入主日志文件级 |
| PgHdr *pager_lookup() | 在哈希表中找出给定页码的页 |

