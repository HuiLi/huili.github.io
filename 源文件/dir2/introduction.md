# select.c简介
&nbsp;&nbsp;&nbsp;&nbsp;SQLite支持SQL语句查询数据库，其中Select查询的结果为零或者多行记录，并且每行记录有固定的列数。一条select语句只会查询数据，并不会对数据库做出更改。其中select.c是将select中子句，合成一个查询树，进行查询记录。在下文中，将逐个介绍select.c中函数的功能及分析。
