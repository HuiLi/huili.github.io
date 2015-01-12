# 分析源代码在SQLite中的地位
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们通过SQLite的体系来简单介绍代码生成器部分分析的源码，下图介绍了分析的源码在SQLite中的地位。
<img src='分析的代码.jpg'>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;语法分析器  在  把标识符组装成完整的SQL语句后，就调用代码生成器产生虚拟机代码，以执行SQL语句请求的工作。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们在代码  生成器这部分分析的文件有：auth.c,alter.c, analyze.c和func.c,这些文件涵盖了大部分最重要、最有意  义的事情。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;auth.c这个  文件用于实现sqlite3_set_authorizer()的功能。 采用了在编译过程中被调用去验证操作用户对其所访问的数  据库中不同地方是否有可读/写的操作权限。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alter.c这个文件是用于实现alter table命令来生成VDBE代码.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;analyze.c这个文件包含代码与分析命令相关。分析命令收集统计信息  表和索引的内容。这些统计数据对查询规划器有用，可以帮助查询规划器做出更好的关于如何执行查询的决策。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;func.c这个  文件包含实现各种SQL函数, 这个文件只有一个出口标志——函数sqliteRegisterBuildinFunctions(),它在文件的底部。
