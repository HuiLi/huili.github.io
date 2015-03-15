# SQLite中几种查询优化做的处理
* SQLite中可用索引的操作

SQLite在处理查询的时候有些语句是不可使用索引的:


1.	操作符：=、>、<、IN等，若操作的属性上有索引，则可以使用；
2.	操作符BETWEEN、LIKE不能用索引。


所以针对某些情况SQLite会进行查询优化使之尽可能的可以使用索引来进行查询。

* BETWEEN语句处理

>原语句：`expr1 BETWEEN expr2 AND expr3 `

        由于BETWEEN语句无法直接使用索引，所以为了尽可能的使用索引，SQLite在该语句后添加了虚项Virtual Terms：expr1 >= expr2 AND expr1 <= expr3。


        Virtual Terms被设置为BETWEEN的子句，如果BETWEEN语句已经编码，那么就忽略子句；如果存在可以使用的索引，则忽略BETWEEN语句，使用AND连接的查询条件处理。

---------------------------------------

* OR语句的特殊情况


>原语句（不可用索引）：`column = expr1 OR column = expr2 OR column = expr3 OR ...`


        针对这种同一个属性的许多等值判断用OR连接起来的语句，SQLite中直接转换为如下形式：column IN (expr1,expr2,expr3,...)。
        这样可以尽量使用列Column上的索引并且减少查询代价。

---------------------------------------
* LIKE语句处理



>原语句：`column LIKE ‘x%’`


        针对这种LIKE语句，SQLite在该语句后添加了虚项Virtual Terms：column >= x AND column < y，其中y与x的长度相同，且除了最后一个字符其他字符相同，y的最后一个字符的ASCII码为x最后一个字符的ASCII码加一。
        例如X LIKE ‘abc%’添加Virtual Terms:X >= ‘abc’ AND X < ‘abd’
