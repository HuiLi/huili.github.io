# 介绍

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite被分为如图所示的三个子系统中的八个大模块，VDBE处于SQLite的核心部分，与它联系的上层是Compiler，下层是Backend后端存储处理。它完成了前端与后端的抽象连接，解释执行字节代码。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;VDBE的字节代码(称为虚拟机语言)由146个操作码(opcodes)构成，主要是进行数据库操作。它的每一条指令都用来完成特定的数据库操作(比如打开一个表的游标)或者为这些操作栈空间的准备(比如压入参数)。 总之，所有的这些指令都是为了满足SQL命令的要求。
<img src="/perface/SQLiteFrame.png" >
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<div align="center">图一 SQLite框架</div>
