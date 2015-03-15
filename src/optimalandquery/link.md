# 连接查询
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在返回查询结果之前，相关表的每行必须都已经连接起来，在SQLite中，这是用嵌套循环实现的，在早期版本中，最左边的是最外层循环，最右边的是最内层循环，连接两个或者更多的表时，如果有索引则放到内层循环中，也就是放到FROM最后面，因为对于前面选中的每行，找后面与之对应的行时，如果有索引则会很快，如果没有则要遍历整个表，这样效率就很低，但在新版本中，这个优化已经实现。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;优化的方法如下：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对要查询的每个表，统计这个表上的索引信息，首先将代价赋值为SQLITE_BIG_DBL（一个系统已经定义的常量）：<br>
**1 如果没有索引，则找有没有在这个表上对rowid的查询条件。**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1．如果有Rowid=EXPR，如果有的话则返回对这个表代价估计，代价计为零，查询得到的记录数为1，并完成对这个表的代价估计。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2．如果没有Rowid=EXPR 但有rowid IN  (...)，而IN是一个列表，那么记录返回记录数为IN列表中元素的个数，估计代价为NlogN。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3．如果IN不是一个列表而是一个子查询结果，那么由于具体这个子查询不能确定，所以只能估计一个值，返回记录数为100，代价为200。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4．如果对rowid是范围的查询，那么就估计所有符合条件的记录是总记录的三分之一，总记录估计为1000000，并且估计代价也为记录数。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5．如果这个查询还要求排序，则再另外加上排序的代价NlogN。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6．如果此时得到的代价小于总代价，那么就更新总代价，否则不更新。<br>
**2 如果WHERE子句中存在OR操作符，那么要把这些OR连接的所有子句分开再进行分析。**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1．如果有子句是由AND连接符构成，那么再把由AND连接的子句再分别分析。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2．如果连接的子句的形式是X<op><expr>，那么就再分析这个子句。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3． 接下来就是把整个对OR操作的总代价计算出来。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4．如果这个查询要求排序，则再在上面总代价上再乘上排序代价NlogN。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5．如果此时得到的代价小于总代价，那么就更新总代价，否则不更新。<br>
**3 如果有索引，则统计每个表的索引信息，对于每个索引**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1．先找到这个索引对应的列号，再找到对应的能用到（操作符必须为=或者是IN（…））这个索引的WHERE子句，如果没有找到，则退出对每个索引的循环，如果找到，则判断这个子句的操作符是什么，如果是=，那么没有附加的代价，如果是IN（sub-select），那么估计它附加代价inMultiplier为25，如果是IN（list），那么附加代价就是N（N为list的列数）。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2． 再计算总的代价和总的查询结果记录数和代价。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3． nRow = pProbe->aiRowEst[i] * inMultiplier;/*计算行数*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4． cost = nRow * estLog(inMultiplier);/*统计代价*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5．如果找不到操作符为=或者是IN（…）的子句，而是范围的查询，那么同样只好估计查询结果记录数为nRow/3，估计代价为cost/3。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6．同样，如果此查询要求排序的话，再在上面的总代价上加上NlogN。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7．如果此时得到的代价小于总代价，那么就更新总代价，否则不更新。<br>
**4 通过上面的优化过程，可以得到对一个表查询的总代价（就是上面各个代价的总和），再对第二个表进行同样的操作，这样如此直到把FROM子句中所有的表都计算出各自的代价，最后取最小的，这将作为嵌套循环的最内层，依次可以得到整个嵌套循环的嵌套顺序，此时正是最优的，达到了优化的目的。**
