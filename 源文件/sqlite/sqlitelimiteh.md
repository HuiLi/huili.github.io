# SqliteLimite.h 数据库源码分析报告
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个头文件定义了SQLite能够处理的变量范围。如TEXT或者   BLOB（ BLOB(binary large object)是一个大文件，典型的BLOB是一张图片或一个声音文件）的最大字节长度，表或者索引中一行的大小等等。<br>
这个头文件一共定义了18个宏，具体定义的宏如下：<br>
无参一般形式#define 标识符 字符串<br>
带参一般形式#define 宏名(形参表) 字符串<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;被定义为“宏”的标识符称为“宏名”。在编译预处理时，对程序中所有出现的“宏名”，都用宏定义中的字符串去代换。 如：<br> #define PI 3.14
<br>#ifndef，这是 if not defined的简写，是宏定义的一种，它是可以根据是否已经定义了一个变量来进行分支选择。条件指示符#ifndef 的最主要目的是防止头文件的重复包含和编译。#endif，终止if。<br>
undef ，是在后面取消以前定义的宏定义。<br>

1.	SQLITE_MAX_LENGTH  最大长度；
2.	SQLITE_MAX_COLUMN  最大列数；如视图中的表、索引、列的最大数，更新语句中SET子句的最大数，选择语句中GROUP BY 或者 ORDER BY子句的最大数，插入语句中VALUES子句的最大数等等。
3.	SQLITE_MAX_SQL_LENGTH  单个SQL语句的最大字节长度；
4.	SQLITE_MAX_EXPR_DEPTH  表达树的最大深度；
5.	SQLITE_MAX_COMPOUND_SELECT  复合的选择语句的最大项数；
6.	SQLITE_MAX_VDBE_OP  虚拟数据库引擎中操作码的最大数；
7.	SQLITE_MAX_FUNCTION_ARG   SQL函数的参数最大数目；
8.	SQLITE_DEFAULT_CACHE_SIZE  内存页用于主数据表的最大数；
9.	SQLITE_DEFAULT_TEMP_CACHE_SIZE  内存页用于临时数据表的最大数；
10.	SQLITE_DEFAULT_WAL_AUTOCHECKPOINT  预写式日志模式下，在数据库检查点之前数据库日志文件中积累的默认侦数；WLA:Write-Ahead Logging预写式日志；
11.	SQLITE_MAX_ATTACHED  添加数据库的最大数。这个值必须在0和62之间。上界是62是因为64位的整型位图利用内存来追踪添加数据库。
12.	SQLITE_MAX_VARIABLE_NUMBER  解析器能接受的通配符的最大值；
13.	SQLITE_MAX_PAGE_SIZE  最大页面尺寸。
14.	SQLITE_DEFAULT_PAGE_SIZE   数据库页的默认大小。如果默认大小大于最大页面尺寸。则把页的默认大小定义为最大页面尺寸。
15.	SQLITE_MAX_DEFAULT_PAGE_SIZE  数据库页的最大默认尺寸。如果最大默认尺寸大于最大页面尺寸。则把页的最大默认尺寸定义为最大页面尺寸。
16.	SQLITE_MAX_PAGE_COUNT  数据库文件的最大页数；
17.	SQLITE_MAX_LIKE_PATTERN_LENGTH
    LIKE或者GLOB中pattern的最大字节长度
    比如：
    elect * from 表
    where 姓名
    like '%李%'      （ '%李%' 就是pattern）
18.	SQLITE_MAX_TRIGGER_DEPTH   触发器的最大深度递归
