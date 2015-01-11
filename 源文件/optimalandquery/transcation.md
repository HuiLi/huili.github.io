# 几个查询优化的转换
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1．对于单个表的单个列而言，如果都有形如T.C=expr这样的子句，并且都是用OR操作符连接起来，形如： x = expr1 OR expr2 = x OR x = expr3 此时由于对于OR，在SQLite中不能利用索引来优化，所以可以将它转换成带有IN操作符的子句：x IN(expr1,expr2,expr3)这样就可以用索引进行优化，效果很明显，但是如果在都没有索引的情况下OR语句执行效率会稍优于IN语句的效率。<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2．如果一个子句的操作符是BETWEEN，在SQLite中同样不能用索引进行优化，所以也要进行相应的等价转换： 如：a BETWEEN b AND c可以转换成：(a BETWEEN b AND c) AND (a>=b) AND (a<=c)。 在上面这个子句中， (a>=b) AND (a<=c)将被设为dynamic且是(a BETWEEN b AND c)的子句，那么如果BETWEEN语句已经编码，那么子句就忽略不计，如果存在可利用的index使得子句已经满足条件，那么父句则被忽略。<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3． 如果一个单元的操作符是LIKE，那么将做下面的转换：x LIKE ‘abc%’，转换成：x>=‘abc’ AND x<‘abd’。因为在SQLite中的LIKE是不能用索引进行优化的，所以如果存在索引的话，则转换后和不转换相差很远，因为对LIKE不起作用，但如果不存在索引，那么LIKE在效率方面也还是比不上转换后的效率的。
