# vdbeaux.c源码概览
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vdbeaux.c包含被虚拟机使用的一些工具，和被库的其他部分用来构建VM程序的一些接口模块。它主要实现了vdbe的创建、销毁和填充。
一个简单的vdbe处理过程如下图所示：
<img src="/vdbeaux/VDBE.png">
<div align="center">图2 vdbe处理过程</div>
sql指令被编译后送达虚拟机进行执行，整个过程大致分为四个部分：由sqlite3VdbeCreate()创建一个vdbe，vdbe创建完成后交由sqlite3VdbeRewin()进行初始化，初始化完成后由sqlite3VdbeMakeRead()为执行做准备，完成上述三步工作后，vdbe最终交由sqlite3VdbeExec()执行。
