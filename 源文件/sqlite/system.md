#体系结构
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在内部，SQLite由以下几个组件组成：内核、SQL编译器、后端以及附件。SQLite通过利用虚拟机和虚拟数据库引擎（VDBE），使调试、修改和扩展SQLite的内核变得更加方便。所有SQL语句都被编译成易读的、可以在SQLite虚拟机中执行的程序集。SQLite支持大小高达2 TB的数据库，每个数据库完全存储在单个磁盘文件中。这些磁盘文件可以在不同字节顺序的计算机之间移动。这些数据以B+树（B+tree）数据结构的形式存储在磁盘上。SQLite根据该文件系统获得其数据库权限。SQLite的体系结构图，如图1-1所示:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（一）公共接口（Interface）

   SQLite库的大部分公共接口由main.c, legacy.c和vdbeapi.c源文件中的函数来实现，这些函数依赖于分散在其他文件中的一些程序，因为在这些文件中它们可以访问有文件作用域的数据结构。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（二）词法分析器（Tokenizer）

   当执行一个包含SQL语句的字符串时，接口程序要把这个字符串传递给tokenizer。Tokenizer的任务是把原有字符串分割成一个个标识符（token），并把这些标识符传递给解析器。Tokenizer是用手工编写的，在C文件tokenize.c中。
<img src="img1-1.jpg">

 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（三） 语法分析器（Parser）

语法分析器的工作是在指定的上下文中赋予标识符具体的含义。SQLite的语法分析器使用Lemon LALR(1)分析程序生成器来产生，Lemon做的工作与YACC/BISON相同，但它使用不同的输入句法，这种句法更不易出错。Lemon还产生可重入的并且线程安全的语法分析器。Lemon定义了非终结析构器的概念，当遇到语法错误时它不会泄露内存。驱动Lemon的源文件可在parse.y中找到。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（四）代码生成器（Code Generator）

   语法分析器在把标识符组装成完整的SQL语句后，就调用代码生成器产生虚拟机代码，以执行SQL语句请求的工作。代码生成器包含许多文件：attach.c, auth.c, build.c, delete.c, expr.c, insert.c,pragma.c, select.c, trigger.c, update.c, vacuum.c和where.c。这些文件涵盖了大部分最重要、最有意义的事情。expr.c处理SQL中表达式的代码生成。where.c处理SELECT、UPDATE和DELETE语句中WHERE子句的代码生成。文件attach.c, delete.c, insert.c, select.c, trigger.c, update.c和vacuum.c处理同名SQL语句的代码生成（这些文件在必要时都调用expr.c和where.c中的例程）。所有其他SQL语句的代码由build.c生成。文件auth.c实现sqlite3_set_authorizer()的功能。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（五）虚拟机（Virtual Machine）

   代码生成器生成的代码由虚拟机来执行。总的来说，虚拟机实现一个专为操作数据库文件而设计的抽象计算引擎。它有一个存储中间数据的存储栈，每条指令包含一个操作码和不超过三个额外的操作数。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（六）B-树（B-Tree）

  一个SQLite数据库使用B-树的形式存储在磁盘上，B-树的实现位于源文件btree.c中。数据库中的每个表和索引使用一棵单独的B-树，所有的B-树存放在同一个磁盘文件中。文件格式的细节被记录在btree.c开头的备注里。B-树子系统的接口在头文件btree.h中定义。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（七）页面高速缓存（Page Cache）

   B-树模块以固定大小的数据块形式从磁盘上请求信息，默认的块大小是1024个字节，但是可以在512和65536个字节之间变化。页面高速缓存负责读、写和缓存这些数据块。页面高速缓存还提供回滚和原子提交的抽象，并且管理数据文件的锁定。B-树驱动模块从页面高速缓存中请求特定的页，当它想修改页面、想提交或回滚当前修改时，它也会通知页面高速缓存。页面高速缓存处理所有麻烦的细节，以确保请求能够快速、安全而有效地被处理。
   页面高速缓存的代码实现被包含在单一的C源文件pager.c中。页面高速缓存子系统的接口在头文件pager.h中定义。

   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; (八) OS接口

   为了在POSIX和Win32操作系统之间提供移植性，SQLite使用一个抽象层来提供操作系统接口。OS抽象层的接口在os.h中定义，每种支持的操作系统有各自的实现：Unix使用os_unix.c，Windows使用os_win.c，等等。每个特定操作系统的实现通常都有自己的头文件，如os_unix.h, os_win.h等。

   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（九）实用工具（Utilities）

  内存分配和字符串比较函数位于util.c中。语法分析器使用的符号表用Hash表来维护，其实现位于hash.c中。源文件utf.c包含Unicode转换子程序。SQLite有自己的printf()实现（带一些扩展功能），在printf.c中，还有自己的随机数生成器，在random.c中。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; （十）测试代码（Test Code）

  如果你计算回归测试脚本，超过一半的SQLite代码将被测试。主要代码文件中有许多assert()语句。另外，源文件test1.c通过test5.c和md5.c实现只用于测试目的的一些扩展。os_test.c后端接口用来模拟断电，以验证页面高速缓存的崩溃恢复机制。
