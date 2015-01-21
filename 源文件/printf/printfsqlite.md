#  Printf分析
我们通过SQLite的体系来简单介绍Printf在SQLite的作用，图一为SQLite的体系结构。

<img src="p1.jpg">
<center>Figure 1. SQLite体系结构.</center>

SQLite被分为如图所示的三个子系统中的八个大模块，在体系结构的顶部编译SQL语句，在中部执行，在底部处理存储于操作系统接口。接口由SQLite C API组成，也就是说不管是程序、脚本语言还是库文件，最终都是通过它与SQLite交互的(我们通常用得较多的ODBC/JDBC最后也会转化为相应C API的调用)。SQLite有自己的printf实现（带一些扩展功能），在printf.c中，还有自己的随机数生成器，在random.c中。
<br>介绍
<br>从1980年代遵循“printf”代码。它是在公共领域。原来这里包括forcompleteness评论。他们都很过时，但作为一个历史参考可能是有用的。大多数的“增强”一直支持这样的功能现在作为标准printf()相同。<br>
该文件包含一组“printf”的例程的代码。这些程序格式字符串，就像从标准C库中的printf()，这里的实施具有增强支持sqllite。<p>
函数包含着 “sqliteInt.h” 头文件
