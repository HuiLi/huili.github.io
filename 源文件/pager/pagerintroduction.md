# Pager简介
Pager主要是用来访问数据磁盘文件，它用一个
日志文件实现自动提交和回滚，它跟数据库文件
是分开的。pager同样实现了文件的锁定，来防
止防止两个进程同时写人数据库文件，或者一个进程读取数据库文件，一个进程写入数据库文件。
<img src = "1.png">

一个connection可以有多个database对象---一个主要的数据库以及附加的数据库，每一个数据库对象有一个B-tree对象，一个B-tree有一个pager对象(这里的对象不是面向对象的“对象”，只是为了说清楚问题)。
<img src = "2.png">

Statement最终都是通过connection的B-tree和pager从数据库读或者写数据，通过B-tree的游标(cursor)遍历存储在页面(page)中的记录。游标在访问页面之前要把数所从disk加载到内存，而这就是pager的任务。

任何时候，如果B-tree需要页面，它都会请求pager从disk读取数据，然后把页面(page)加载到页面缓冲区(page cache)，之后，B-tree和与之关联的游标就可以访问位于page中的记录了。如果cursor改变了page，为了防止事务回滚，pager必须采取特殊的方式保存原来的page。
