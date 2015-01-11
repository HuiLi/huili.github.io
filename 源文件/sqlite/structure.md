# SQLite的体系结构
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在内部，SQLite由以下几个组件组成：内核、SQL编译器、后端以及附件。SQLite通过利用虚拟机和虚拟数据库引擎（VDBE），使调试、修改和扩展SQLite的内核变得更加方便。图1.1是SQLite的体系结构，显示了SQLite的主要组件以及各组件之间是如何相互关联的。
<img src="SQLiteStruct.jpg">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**图1.1SQLite的体系结构**
