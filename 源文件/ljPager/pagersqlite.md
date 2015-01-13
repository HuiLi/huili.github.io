# Pager在SQLite中的地位及作用

<img src="lj1.png">
图一：SQLite体系结构</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;图一是SQLite官网对SQLite体系结构的描述，在SQLite实现中，无论是代码的设计还是数据结构的设计都是按上述体系结构完成的，层次非常清晰。SQLite分为三部分，有Core（这个里面包含Compiler），Backend和Accessories，而Pager部分就在Backend里面，当数据库文件被打开，并且创建一个B-Tree之后，Pager就开始工作。分配并初始化一个新Pager对象，将其指针放到ppPager。该pager会在调用sqlite3PagerClose()时被释放。zFilename参数是要打开的数据库文件的路径。如果zFilename为空，创建随机文件名的文件。如果zFilename为":memory:"，所有信息都放到缓冲区中，不会被写入磁盘。这用来实现内存数据库。如果pager对象已分配且指定文件打开成功，返回SQLITE_OK并将ppPager指向新pager对象。如果有错误发生，*ppPager置空并返回错误代码。Pager的执行过程是先申请空间，再调用sqlite3OsOpen()打开文件（如果需要），再根据打开的文件设置内存。

<img src="lj2.png">

图2 SQLite内存数据结构</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;图二其实就是SQLite中一个整体的运行流程，Pager部分其实就是页面缓存的子系统的实现。Pager用于访问数据库磁盘文件，它通过使用独立于数据库文件的日志文件来实现原子事务的提交和回滚。页面还实现了文件锁,以防止两个进程同时写相同的数据库文件,或者当一个进程从数据库中读的时候另一个在写。同时当使用一个回滚日志时要保持不变性，当编译synchronous=OFF时，页面被认为是一旦写下来就自动同步；否则直到VFS的xSync方法在包含页面的文件上被成功调用，页面才会同步。
