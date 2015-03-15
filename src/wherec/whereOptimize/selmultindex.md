# 多重索引的选择
SQLite中，一般情况下（除了OR子句的优化查询外），对于每一个表，在查询的FROM子句中至多使用一个索引，并且SQLite努力在每个表中使用至少一个索引。而很多的表中，会创建多个索引，SQLite需要判断使用哪个索引较为合理。<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite在面临多个索引的选择是，它会试图评价每种方案执行查询时使用的工作总额。然后它选出一种工作总而最少的方案。而使用ANALYZE命令可以帮助SQLite更加精确的评估使用多种索引的工作情况。<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当然SQLite也提供了手动取消索引的方法，就是在WHERE子句中的索引列前加上“+”来阻止使用该索引列。<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如：
<pre>
        SELECT z FROM ex2 WHERE +x=5 AND y=6;
</pre>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;上述例子中就手动的取消了x上的索引。<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于范围查询，当SQLite被SQLITE_ENABLE_STAT3或SQLITE_ENABLE_STAT4编译后，如果某列的查询空间缩减倍数较大，并且该列是一个索引列，那么SQLite就会使用该索引。因为SQLITE_ENABLE_STAT3或SQLITE_ENABLE_STAT4选项会使ANALYZE命令来收集在sqlite_stat3或sqlite_stat4表中一个关于列内容的直方图，并且使用这个直方图来更好的分析范围查询，并且选择最好的查询优化。对于STAT3记录的直方图数据只有最左边的索引列，而STAT4记录的直方图数据时所有的索引列。所以，对于只有一个索引列的情况，STAT3和STAT4的工作结果是相同的。但是，如果范围约束的列不是最左边的索引列，那么STAT3收集的信息对于选择索引来说将是无用的。<p>
程序实现如下：
<img src="selmultindex.png"/>
