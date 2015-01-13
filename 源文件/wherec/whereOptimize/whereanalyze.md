# WHERE子句分析
SELECT语句中如果存在WHERE子句，则对WHERE子句进行分析。首先根据AND关键字进行分割，之后对每个分割后的子句进行分析。对于分割后的子句，如果是以下情况：
<pre>
column = expression
column > expression
column >= expression
column < expression
column <= expression
expression = column
expression > column
expression >= column
expression < column
expression <= column
column IN (expression-list)
column IN (subquery)
column IS NULL
</pre>
那么这个子句是可以使用索引进行查询优化的。<br>
如果定义一个索引，<br>
<pre>
    CREATE INDEX idx_ex1 ON ex1(a, b, c, d, e,..., y, z)；
</pre>
那么如果索引中的初始列（列a, b等）出现在分割后的子句中，那么索引就会被启用，但是不能间断的使用索引列。如：WHERE子句中没有列c的约束，那么只有列a，b是可以使用索引的，其余的索引列都将无法正常使用。

执行步骤和程序实现如下：
<img src="whereAnalyze.png"/>
