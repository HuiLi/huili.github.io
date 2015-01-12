# sqlite3WhereEnd函数
该函数的主要用处是生成OPCode中Where循环的结束部分代码以及释放资源等，结尾操作大致如下：
* 生成VDBE中收尾的OPCode；
* 关闭所有在sqlite3WhereBegin中打开的游标；
* 释放WhereInfo的资源；
