# SQLite简介
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite，是一款轻型的数据库，是遵守ACID的关系数据库管理系统，SQLite是D.RichardHipp用C语言编写的开源嵌入式数据库引擎。它是完全独立的，不具有外部依赖性。它是作PHPV4.3中的一个选项引入的，构建在PHPV5中。SQLite支持多数SQL92标准，可以在所有主要的操作系统上运行，并且支持大多数计算机语言。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite 对 SQL92 标准的支持包括索引、限制、触发和查看。SQLite 不支持外键限制，但支持原子的、一致的、独立的和持久的 (ACID) 事务。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;区别于其他数据库引擎，SQLite引擎不是一个独立的进程，而是根据应用程序要求进行动态或静态连接到程序中成为它的一个主要部分。SQLite直接访问其存储文件。整个数据库(定义、表、索引和数据本身)都在宿主主机上存储在一个单一的文件中。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;它包含在一个简介的C库中。它的设计目标是嵌入式的，而且目前已经在很多嵌入式产品中使用了它，它占用资源非常的低，在嵌入式设备中，可能只需要几百K的内存就够了。它能够支持Windows/Linux/Unix
等等主流的操作系统，同时能够跟很多程序语言相结合，比如 Tcl、C#、PHP、Java等，还有ODBC接口，与Mysql、PostgreSQL这两款开源的世界著名数据库管理系统相比，它的处理速度更快。SQLite第一个Alpha版本诞生于2000年5月。 至今已经有14个年头，SQLite也迎来了一个版本 SQLite 3已经发布。
