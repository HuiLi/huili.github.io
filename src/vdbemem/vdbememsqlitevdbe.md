# SQLite的虚拟机
## 1、虚拟机（Virtual Machine）

VDBE是SQLite的核心，它的上层模块和下层模块都是本质上都是为它服务的。它的实现位于vbde.c, vdbe.h, vdbeapi.c, vdbeInt.h, 和vdbemem.c几个文件中。它通过底层的基础设施B+Tree执行由编译器（Compiler）生成的字节代码，这种字节代码程序语言(bytecode programming lauguage)是为了进行查询，读取和修改数据库而专门设计的。
字节代码在内存中被封装成sqlite3_stmt对象(内部叫做Vdbe，见vdbeInt.h)，Vdbe（或者说statement）包含执行程序所需要的一切：
a)    a bytecode program
b)    names and data types for all result columns
c)    values bound to input parameters
d)    a program counter
e)    an execution stack of operands
f)    an arbitrary amount of "numbered" memory cells
g)    other run-time state information (such as open BTree objects, sorters, lists, sets)

字节代码和汇编程序十分类似，每一条指令由操作码和三个操作数构成：<opcode, P1, P2, P3>。Opcode为一定功能的操作码，为了理解，可以看成一个函数。P1是32位的有符号整数，p2是31位的无符号整数，它通常是导致跳转(jump)的指令的目标地址（destination），当然还有其它用途；p3为一个以null结尾的字符串或者其它结构体的指针。和C API不同的是，VDBE操作码经常变化，所以不应该用字节码写程序。
下面的几个C API直接和VDBE交互：
• sqlite3_bind_xxx() functions
• sqlite3_step()
• sqlite3_reset()
• sqlite3_column_xxx() functions
• sqlite3_finalize()

为了有个感性，下面看一个具体的字节码程序：
<img src="Copc.png">
