# SQLite简介
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite，是一款轻型的数据库，是遵守ACID的关系数据库管理系统，SQLite是D.RichardHipp用C语言编写的开源嵌入式数据库引擎。它是完全独立的，不具有外部依赖性。它是作PHPV4.3中的一个选项引入的，构建在PHPV5中。SQLite支持多数SQL92标准，可以在所有主要的操作系统上运行，并且支持大多数计算机语言。SQLite还非常健壮。其创建者保守地估计SQLite可以处理每天负担多达100,00次点击率的Web站点，并且SQLite有时候可以处理10倍于上述数字的负载。同样比起Mysql、PostgreSQL这两款开源世界著名的数据库管理系统来讲，它的处理速度比他们都快。SQLite 对 SQL92 标准的支持包括索引、限制、触发和查看。SQLite 不支持外键限制，但支持原子的、一致的、独立的和持久的 (ACID) 事务。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;不像常见的客户-服务器范例，SQLite引擎不是个程序与之通信的独立进程，而是连接到程序中成为它的一个主要部分。所以主要的通信协议是在编程语言内的直接API调用。这在消耗总量、延迟时间和整体简单性上有积极的作用。整个数据库(定义、表、索引和数据本身)都在宿主主机上存储在一个单一的文件中。它的简单的设计是通过在开始一个事务的时候锁定整个数据文件而完成的。<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite，是一款轻型的数据库，是遵守ACID的关系型数据库管理系统，它包含在一个相对小的C库中。它是D.RichardHipp建立的公有领域项目。它的设计目标是嵌入式的，而且目前已经在很多嵌入式产品中使用了它，它占用资源非常的低，在嵌入式设备中，可能只需要几百K的内存就够了。它能够支持Windows/Linux/Unix
等等主流的操作系统，同时能够跟很多程序语言相结合，比如 Tcl、C#、PHP、Java等，还有ODBC接口，同样比起Mysql、PostgreSQL这两款开源的世界著名数据库管理系统来讲，它的处理速度比他们都快。SQLite第一个Alpha版本诞生于2000年5月。 至今已经有14个年头，SQLite也迎来了一个版本 SQLite 3已经发布。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite是一个进程内的库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。它是一个零配置的数据库，这意味着与其他数据库一样，不需要在系统中配置。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;就像其他数据库，SQLite 引擎不是一个独立的进程，可以按应用程序需求进行静态或动态连接。SQLite 直接访问其存储文件。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite的特性主要有：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1）ACID事务。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2）零配置--无需安装和管理配置。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3）储存在单一磁盘文件中的一个完整的数据库。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4）数据库文件可以在不同字节顺序的机器间自由的共享。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5）支持数据库大小至2TB。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6）足够小, 大致13万行C代码, 4.43M。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7）比一些流行的数据库在大部分普通数据库操作要快。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8）简单, 轻松的API。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;9）包含TCL绑定, 同时通过Wrapper支持其他语言的绑定。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;10）良好注释的源代码, 并且有着90%以上的测试覆盖率。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11）独立: 没有额外依赖。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;12）源码完全的开源, 你可以用于任何用途, 包括出售它。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;13）支持多种开发语言,C, PHP, Perl, Java, C#,Python, Ruby。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;14）SQLite虽然很小巧，但它支持的sql很多，比如：ATTACH DATABASE，BEGIN TRANSACTION，COMMENT等等，同时它还支持事务处理功能。
