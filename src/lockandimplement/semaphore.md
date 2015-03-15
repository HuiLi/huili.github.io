# Named Semaphore Locking
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Named Semaphore Locking（命名信号锁）其实就像点文件锁和聚集锁一样，实际上只支持排他锁。只有单个进程可以在同一时间对数据库文件进行读或写。这虽然减少了数据库潜在的并发性，却使得这种锁的实现更加容易。但是，只有VxWorks操作系统支持命名信号锁。
