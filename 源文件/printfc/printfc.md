# Printf.c简介
# 结构
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;printf.c是实用工具（Utilities）的一部分，Utilities在SQLite数据库中的地位如下图所示。Utilities中，内存分配和字符串比较函数位于util.c中。语法分析器使用的符号表用Hash表来维护，其实现位于hash.c中。源文件utf.c包含Unicode转换子程序。SQLite有自己的printf()实现（带一些扩展功能），在printf.c中，还有自己的随机数生成器，在random.c中。
![](5.png)

