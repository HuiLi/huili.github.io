# flock Locking
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;flock Locking（聚集锁）在SQLite支持各种粒度锁级别紧缩成一个单独的排他锁，这一点上和点文件锁是一样的。换句话说，共享锁，保留锁和未决锁对于排他锁来说是一样的。当你使用聚集锁时，SQLite仍然会正常工作,但是并发性降低,因为同一时间只有一个进程可以读取数据库。
