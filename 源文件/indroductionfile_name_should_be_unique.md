# SQLite体系结构简介
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite是D.Richard Hipp用C语言编写的开源嵌入式数据库引擎。它支持大多数的SQL92标准，并且可以在所有主要的操作系统上运行。<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite由以下几个部分组成：SQL编译器、内核、后端以及附件。SQLite通过利用虚拟机和虚拟数据库引擎(VDBE)，是调试、修改和扩展SQLite的内核变得更加方便。所有SQL语句都被编译成易读的、可以在SQLite虚拟机中执行的程序集。SQLite的整体结构图如下：
<br></br><img src="structure.jpg">
