# Pager状态
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从上图看B-tree和pager是相连接的，并且在事务和锁上起着关键作用。一个连接可以有多个数据库对象---一个主要的数据库以及附加的数据库，每一个数据库对象有一个B-tree对象，一个B-tree有一个pager对象。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;状态最终都是通过连接的B-tree和pager从数据库读/写数据，通过B-tree的游标遍历存储在页面中的记录。游标在访问页面之前要把数所从磁盘加载到内存，而这就是页面的任务。任何时候，如果B-tree需要页面，它都会请求页面从磁盘中读取数据，然后把页面(page)加载到页面缓冲区，之后B-tree和与之关联的游标就可以访问位于页面中的记录了。Pager.eState可变的存储页面的当前‘state’。一个页面可能下列七个状态中的一个（打开，读取，写锁，错误，写缓存模式，写数据库模式，写结束）。事务状态的列表展示如下：

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;打开——读取（加一个共享锁）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;读取——打开（页面解锁）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;读取——写锁（在页面上开始一个写事务）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;写锁——写缓存模式（页面打开日志）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;写缓存模式——写数据库模式（同步日志）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;写数据库模式——写结束（把页面同步到数据库文件中）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;写**——读取（页面结束进程）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;写**——错误（页面错误）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;错误——打开（页面解锁）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果游标改变了页面，为了防止事务回滚，页面必须采取特殊的方式保存原来的page。总的来说，pager负责读写数据库，管理内存缓存和页面（page），以及管理事务，锁和崩溃恢复。
