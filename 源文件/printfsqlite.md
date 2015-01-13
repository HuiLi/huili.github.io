#  Printf()在SQLite中的位置
我们通过SQLite的体系来简单介绍Printf在SQLite的作用，图一为SQLite的体系结构。

<img src="p1.jpg">
<center>Figure 1. SQLite体系结构.</center>

SQLite被分为如图所示的三个子系统中的八个大模块，在体系结构的顶部编译SQL语句，在中部执行，在底部处理存储于操作系统接口。接口由SQLite C API组成，也就是说不管是程序、脚本语言还是库文件，最终都是通过它与SQLite交互的(我们通常用得较多的ODBC/JDBC最后也会转化为相应C API的调用)。SQLite有自己的printf实现（带一些扩展功能），在printf.c中，还有自己的随机数生成器，在random.c中。
