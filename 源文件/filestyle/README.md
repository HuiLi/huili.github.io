# Wal文件格式
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WAL日志包括一个头和0到多个框（frames），每个框记录一个页（page）修改的内容。对数据库所有的修改都通过修改框的方式写入WAL日志。单个的WAL日志可记录多个事务。WAL日志中的内容会定期修改到数据库文件中，这个操作成为检查点。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;单个WAL日志可多次使用。也就是说，WAL可以被框占满，当检查点动作过后，其他的框又能写到原来的位置上。WAL日志总是从开始到结尾顺序增长。附加到框后面的检查总数（checksums）和计数（counters）用来确定WAL中的那些框有效，哪些框是以前检查点动作操作完成的。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WAL头有32字节长，包括以下8个32位的大端无符号整数<br>
* 0: Magic number. 0x377f0682 or 0x377f0683 <br>

* 4: File format version. Currently 3007000<br>

* 8: Database page size. Example: 1024<br>

* 12: Checkpoint sequence number<br>

* 16: Salt-1, random integer incremented with each checkpoint<br>

* 20: Salt-2, a different random integer changing with each ckpt<br>

* 24: Checksum-1 (first part of checksum for first 24 bytes of header).<br>

* 28: Checksum-2 (second part of checksum for first 24 bytes of header).<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在Wal.c中实现：<br>
&nbsp;&nbsp;&nbsp;struct WalIndexHdr {<br>
  &nbsp;&nbsp;&nbsp;u32 iVersion;<br>                &nbsp;&nbsp;&nbsp;wal索引版本<br>
  &nbsp;&nbsp;&nbsp;u32 unused;                      未使用区域<br>
  &nbsp;&nbsp;&nbsp; u32 iChange;                    每次事务的计数器<br>
  &nbsp;&nbsp;&nbsp;u8 isInit;   <br>
  &nbsp;&nbsp;&nbsp;u8 bigEndCksum;                 判断wal中的checksum的类型是否为big-endian <br>
  &nbsp;&nbsp;&nbsp;u16 szPage;                     数据库page数量<br>
  &nbsp;&nbsp;&nbsp;u32 mxFrame;                    WAL中最后一个有效帧的索引<br>
  &nbsp;&nbsp;&nbsp;u32 nPage;                      数据库中的页大小<br>
  &nbsp;&nbsp;&nbsp;u32 aFrameCksum[2];             日志最后一帧的checksum<br>
  &nbsp;&nbsp;&nbsp;u32 aSalt[2];                   从WAL头复制两个salt值<br>
  &nbsp;&nbsp;&nbsp;u32 aCksum[2]; <br>
 &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;预写日志文件是由下列对象的一个实例来表示。
 &nbsp;&nbsp;&nbsp;struct Wal {<br>
  &nbsp;&nbsp;&nbsp; sqlite3_vfs *pVfs;          //用来创建pDbFd的VFS<br>
 &nbsp;&nbsp;&nbsp;  sqlite3_file *pDbFd;        //数据库文件<br>
  &nbsp;&nbsp;&nbsp; sqlite3_file *pWalFd;       //WAL文件<br>
  &nbsp;&nbsp;&nbsp; u32 iCallback;              //传递给回调日志的值<br>
  &nbsp;&nbsp;&nbsp; i64 mxWalSize;              //复位后调整WAL到这个大小<br>
  &nbsp;&nbsp;&nbsp; int nWiData;                //apWiData数组的大小 <br>
 &nbsp;&nbsp;&nbsp; int szFirstBlock;           //写入WAL文件的第一个块的大小<br>
  &nbsp;&nbsp;&nbsp; uolatile u32 ** apWiData ; //指向内存中WAL索引内容<br>
 &nbsp;&nbsp;&nbsp;  u32 szPage ;             //数据库页面大小<br>
 &nbsp;&nbsp;&nbsp;  i16 readLock ;           //读哪个锁正在举行。 -1表示无<br>
 &nbsp;&nbsp;&nbsp;  u8 syncFlags ;           //标志使用同步头写<br>
 &nbsp;&nbsp;&nbsp;  u8 exclusiveMode ;      //非零，如果连接是独占模式<br>
 &nbsp;&nbsp;&nbsp;  u8 writeLock ;          //如果真在写事务<br>
 &nbsp;&nbsp;&nbsp;  u8 ckptLock ;          //如果真拿着一个检查站锁定<br>
 &nbsp;&nbsp;&nbsp;  u8 readonly;          //WAL_RDWR ， WAL_RDONLY ，或WAL_SHM_RDONLY <br>
 &nbsp;&nbsp;&nbsp;  u8 truncateOnCommit ; //真正截断WAL上提交的文件<br>
 &nbsp;&nbsp;&nbsp;  u8 syncHeader ;      //FSYNC沃尔玛头如果为true <br>
 &nbsp;&nbsp;&nbsp;  u8 padToSectorBoundary //垫记录到下一个扇区<br>
 &nbsp;&nbsp;&nbsp;  walIndexHdr HDR ;    //为当前事务wal索引头<br>
 &nbsp;&nbsp;&nbsp;  const char * zWalName ;  //WAL文件的文件名<br>
 &nbsp;&nbsp;&nbsp;  u32 nCkpt ;         //wal检查点序列计数器\<br>
＃ IFDEF SQLITE_DEBUG<br>
 &nbsp;&nbsp;&nbsp;  u8 LOCKERROR ;      //发生锁定错误时<br>
 &nbsp;&nbsp;&nbsp;＃ ENDIF<br>
 &nbsp;&nbsp;&nbsp;} ;<br>
