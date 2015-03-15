# 简析SQLite的锁机制
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite在 windows平台上的使用来自于os_win.c文件的实现，但要实现对数据库的操作，其核心在于SQLite的锁机制。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite采用粗放型的锁。当一个连接要写数据库时，所有其他的连接被锁住，直到写连接结束了它的事务。SQLite有一个加锁表，来帮助不同的写数据库者能够在最后一刻再加锁，以保证最大的并发性。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite使用锁逐步上升机制，为了写数据库，连接需要逐步地获得排它锁。对于5个不同的锁状态：未加锁(UNLOCKED)、共享(SHARED)、保留(RESERVED)、未决(PENDING)和排它(EXCLUSIVE)。每个数据库连接在同一时刻只能处于其中一个状态。每种状态(未加锁状态除外)都有一种锁与之对应。锁的状态以及状态的转换如下图所示：
<img src="4.png">
**锁类型**：

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在SQLite中为了写数据库，连接需要逐步地获得排它锁。SQLite有5个不同的锁：未加锁(NO_LOCK)、共享锁(SHARED_LOCK)、保留锁(RESERVED_LOCK)、未决锁(PENDING_LOCK)和排它锁(EXCLUSIVE_LOCK)。

**NO_LOCK**：最初的状态是未加锁状态，在此状态下，连接还没有存取数据库。当连接到了一个数据库，甚至已经用BEGIN开始了一个事务时，连接都还处于未加锁状态。

**SHARED锁**：SHARED锁意味着进程要读（不写）数据库。一个数据库上可以同时有多个进程获得SHARED锁，哪个进程能够在SHARED_FIRST区域加共享锁(使用LockFileEx()LockFileEx()函数)，即获得了SHARED锁。

**RESERVED锁**： RESERVED锁意味着进程将要对数据库进行写操作。一个数据库上同时只能有一个进程拥有RESERVED锁。RESERVED锁可以与SHARED锁共存，并可以继续对数据库加新的SHARED锁。


**PENDING锁**：PENDING LOCK意味着进程已经完成缓存中的数据修改，并想立即将更新写入磁盘。它将等待此时已经存在的读锁事务完成，但是不允许对数据库加新的SHARED LOCK(这与RESERVED LOCK相区别)。


**EXCLUSIVE锁**：在此锁状态下，进程此时就可以自由地对数据库进行修改了，所有以前对缓冲区所做的修改都会被写到数据库文件。
