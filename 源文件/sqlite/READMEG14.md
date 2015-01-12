# SQLite的介绍
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite是一个软件库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。SQLite是一个增长最快的数据库引擎，这是在普及方面的增长，与它的尺寸大小无关。SQLite源代码不受版权限制。
<img src='SQLite体系结构.jpg'>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在内部，SQLite由以下几个组件组成：内核、SQL编译器、后端。SQLite通过利用虚拟机和虚拟数据库引擎（VDBE），使调试、修改和扩展SQLite的内核变得更加方便。所有SQL语句都被编译成易读的、可以在SQLite虚拟机中执行的程序集。SQLite支持大小高达2TB的数据库，每个数据库完全存储在单个磁盘文件中。这些磁盘文件可以在不同字节顺序的计算机之间移动。这些数据以B+树（B+tree）数据结构的形式存储在磁盘上。SQLite根据该文件系统获得其数据库权限。
