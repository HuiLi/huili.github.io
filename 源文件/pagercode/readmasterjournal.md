# readMasterJournal
    static int readMasterJournal(sqlite3_file         *pJrnl, char *zMaster, u32 nMaster){
    int rc;
    u32 len;
    i64 szJ;
     u32 cksum;
    u32 u;
    unsigned char aMagic[8];
    zMaster[0] = '\0';
    if( SQLITE_OK!=(rc = sqlite3OsFileSize(pJrnl,     &szJ))
    || szJ<16
    || SQLITE_OK!=(rc = read32bits(pJrnl, szJ-16,     &len))
    || len>=nMaster
    || SQLITE_OK!=(rc = read32bits(pJrnl, szJ-12,     &cksum))
     || SQLITE_OK!=(rc = sqlite3OsRead(pJrnl,         aMagic, 8, szJ-8))
     || memcmp(aMagic, aJournalMagic, 8)
    || SQLITE_OK!=(rc = sqlite3OsRead(pJrnl,         zMaster, len, szJ-16-len))
    ){
        return rc;
    }
    for(u=0; u<len; u++){
        cksum -= zMaster[u];
    }
    if( cksum ){
    len = 0;
    }
      zMaster[len] = '\0';
      return SQLITE_OK;
    }
当日志文件被执行pPager必须处于打开状态。这个函数尝
试从文件末尾读取主日志文件名，如果成功将其调入内存
中。看上面writeMasterJournal评论（）的格式常常在一个日志文件的末尾存储一个主日志文件名。zMaster必须指向至少nnMaster字节的缓冲区。如果主日志名字比 nMaster字节还长，那么如果没有主日志名已经存在日志中这将被处理。*如果主日志文件名已经存在日志文件的末尾，然后然后将其复制到缓冲区指向ZMASTER。一个终止字
符将被添加到缓存区中日志文件的末尾。如果没有确定没
有主日志文件名存在，zMaster[0]将被设置成0并且返回SQLITE_OK。如果从日志文件读取时发生错误，那么错误代码将被返回。

