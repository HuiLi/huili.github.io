    # writeMasterJournal
    static int writeMasterJournal(Pager *pPager, const char *zMaster){
      int rc;                          /* Return code */
      int nMaster;                     /* Length of string zMaster */
      i64 iHdrOff;                     /* Offset of header in journal file */
      i64 jrnlSize;                    /* Size of journal file on disk */
      u32 cksum = 0;                   /* Checksum of string zMaster */

      assert( pPager->setMaster==0 );
      assert( !pagerUseWal(pPager) );

      if( !zMaster
       || pPager->journalMode==PAGER_JOURNALMODE_MEMORY
       || pPager->journalMode==PAGER_JOURNALMODE_OFF
      ){
        return SQLITE_OK;
      }
      pPager->setMaster = 1;
      assert( isOpen(pPager->jfd) );
      assert( pPager->journalHdr <= pPager->journalOff );
    if( pPager->fullSync ){
        pPager->journalOff = journalHdrOffset(pPager);
      }
      iHdrOff = pPager->journalOff;
    if( (0 != (rc = write32bits(pPager->jfd, iHdrOff, PAGER_MJ_PGNO(pPager))))
       || (0 != (rc = sqlite3OsWrite(pPager->jfd, zMaster, nMaster, iHdrOff+4)))
       || (0 != (rc = write32bits(pPager->jfd, iHdrOff+4+nMaster, nMaster)))
       || (0 != (rc = write32bits(pPager->jfd, iHdrOff+4+nMaster+4, cksum)))
       || (0 != (rc = sqlite3OsWrite(pPager->jfd, aJournalMagic, 8, iHdrOff+4+nMaster+8)))
      ){
        return rc;
      }
      pPager->journalOff += (nMaster+20);

    if( SQLITE_OK==(rc = sqlite3OsFileSize(pPager->jfd, &jrnlSize))
       && jrnlSize>pPager->journalOff
      ){
        rc = sqlite3OsTruncate(pPager->jfd, pPager->journalOff);
      }
      return rc;
    }
将主日志文件名写入之日文件中pPager所指向的当前所在
的位置。
主日志必须最后写入一个日志文件。如果页面处于同步模式，在一切被写入之前，日志文件的描述符前进到下一个扇区边界。
格式如下：

（1） 4个字节：PAGER_MJ_PGNO

（2）N个字节：主日志文件名必须使用utf-8

（3）4个字节：主日志文件名的长度，没有终止符

（4）4个字符：主主日志名校验

（5）8个字节：aJournalMagic[]

主日志校验是所有日志文件名的字节总和，其中每个字节
被解释为一个有符号的8位整数。
如果zMaster是一个空指针，这被称为一个无用的操作。
