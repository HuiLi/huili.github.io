# 结果代码
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;31个结果代码在sqlite3.h定义，按照字母顺序列出：
<img src="1.jpg">
1）SQLITE_BUSY：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite作为一款小型的嵌入式数据库，本身没有提供复杂的锁定机制，无法内部管理多路并发下的数据操作同步问题，更谈不上优化，所以涉及到多路并发的情况，需要外部进行读写锁控制，否则SQLite会返回SQLITE_BUSY错误，以驳回相关请求。SQLITE_BUSY结果代码表示数据库文件不能写入（或在某些情况下被读出）。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;例如，如果进程A正在进行写事务处理的时候，进程B试图启动一个新的写事务，那么进程B将被返回一个SQLITE_BUSY结果，因为SQLite在同一时刻仅支持一个写操作。<br>
2）SQLITE_LOCKED：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLITE_LOCKED结果码表明一个写操作无法继续，因为相同的数据库连接或不同的数据库连接使用了共享缓存造成了冲突。例如，当另一个线程正在从表中读取相同的数据库连接，那么DROP TABLE语句将不能执行。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLITE_LOCKED结果代码不同于SQLITE_BUSY，SQLITE_BUSY表示了不同的数据库连接上的冲突，它可能发生在不同的进程中。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一条语句可以处于不同状态，对于一条新的或者刚刚被reset的语句，它们处于“ready”状态，代表它们已经准备好执行，但还没有开始执行。一条语句也有可能处于“running”状态，表明这条语句已经开始执行，但还没有完成。还有一种状态叫做“done”，表明一条语句已经执行完成。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于有些API函数，只能在某条语句处于特定状态下才可以执行，比如sqlite3_bind_xxx函数，只有在一条语句处于“ready”状态时才可以被调用，否则函数将会返回SQLITE_MISUSE错误码。下图展示了一条语句所处于的不同状态，以及不同状态之间是如何转换的。
<img src="2.jpg">
