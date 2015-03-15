# SQLite中SQL语句执行步骤
给定一个SQL语句,SQLite首先对其词法分析（该部分手动编码实现）、语法分析（使用第三方文法分析器生成工具Lemon）以及语义分析（sqlite3SelectNew()中生成SELECT结构体，在sqlite2Select()中处理）。
<br>然后生成执行计划：
<br>（a）处理对象：提取查询语法树的FROM部分生成的语法树（包含FROM中表的信息），WHERE部分的语法树（包含WHERE中所有表达式的信息）组成的pTabList语法树。
<br>（b）处理结果：sqlite3WhereBegin()完成where的优化，并生成opcode.
<br>（c）处理方式：
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(1)每张表（FROM子句中的）都是写成嵌套循环。循环的三种扫描方式：全表，索引扫描（自定义创建的索引）和rowid(默认递增id，聚簇索引)扫描。
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(2)上一步的嵌套循环利用贪心算法，计算where表达式能使用的策略及代价，得出每层的最佳扫描方式和扫描层次的顺序（即扫描表的顺序和方式）
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(3)codeOneLoopStart()根据扫描表的顺序和方式生成每层循环的opcode
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(4)针对不同的查询策略，生成各自不同的opcode.
