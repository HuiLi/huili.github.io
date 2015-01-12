# SQLite简介
## 1、SQLite介绍

SQLite是目前最流行的开源嵌入式数据库，和很多其他嵌入式存储引擎相比(NoSQL)，如BerkeleyDB、MemBASE等，SQLite可以很好的支持关系型数据库所具备的一些基本特征，如标准SQL语法、事务、数据表和索引等。事实上，尽管SQLite拥有诸多关系型数据库的基本特征，然而由于应用场景的不同，它们之间并没有更多的可比性。下面我们将列举一下SQLite的主要特征：

1). 管理简单，甚至可以认为无需管理。

2). 操作方便，SQLite生成的数据库文件可以在各个平台无缝移植。

3). 可以非常方便的以多种形式嵌入到其他应用程序中，如静态库、动态库等。

4). 易于维护。

综上所述，SQLite的主要优势在于灵巧、快速和可靠性高。SQLite的设计者们为了达到这一目标，在功能上作出了很多关键性的取舍，与此同时，也失去了一些对RDBMS关键性功能的支持，如高并发、细粒度访问控制(如行级锁)、丰富的内置函数、存储过程和复杂的SQL语句等。正是因为这些功能的牺牲才换来了简单，而简单又换来了高效性和高可靠性。

## 2、架构(architecture)

SQLite采用了模块的设计，它由三个子系统，包括8个独立的模块构成。
<img src="liucheng.jpg">
### 2.1、接口(Interface)

接口由SQLite C API组成，也就是说不管是程序、脚本语言还是库文件，最终都是通过它与SQLite交互的(我们通常用得较多的ODBC/JDBC最后也会转化为相应C API的调用)。
### 2.2、编译器(Compiler)

在编译器中，分词器（Tokenizer）和分析器(Parser)对SQL进行语法检查，然后把它转化为底层能更方便处理的分层的数据结构---语法树，然后把语法树传给代码生成器(code generator)进行处理。而代码生成器根据它生成一种针对SQLite的汇编代码，最后由虚拟机(Virtual Machine)执行。
### 2.3、虚拟机(Virtual Machine)


架构中最核心的部分是虚拟机，或者叫做虚拟数据库引擎(Virtual Database Engine,VDBE)。它和Java虚拟机相似，解释执行字节代码。VDBE的字节代码由128个操作码(opcodes)构成，它们主要集中在数据库操作。它的每一条指令都用来完成特定的数据库操作(比如打开一个表的游标)或者为这些操作栈空间的准备(比如压入参数)。总之，所有的这些指令都是为了满足SQL命令的要求.
### 2.4、后端(Back-End)

后端由B-树(B-tree)，页缓存(page cache，pager)和操作系统接口(即系统调用)构成。B-tree和page cache共同对数据进行管理。B-tree的主要功能就是索引，它维护着各个页面之间的复杂的关系，便于快速找到所需数据。而pager的主要作用就是通过OS接口在B-tree和Disk之间传递页面。

## 3、SQLite的特点(SQLite’s Features and Philosophy)

### 3.1、零配置(Zero Configuration)

### 3.2、可移植(Portability)：

它是运行在Windows,Linux,BSD,Mac OS X和一些商用Unix系统，比如Sun的Solaris,IBM的AIX，同样，它也可以工作在许多嵌入式操作系统下，比如QNX,VxWorks,Palm OS, Symbin和Windows CE。
### 3.3、Compactness：
SQLite是被设计成轻量级，自包含的。one header file, one library, and you’re relational, no external database server required
### 3.4、简单(Simplicity)
3.5、灵活(Flexibility)
3.6、可靠(Reliability)：

