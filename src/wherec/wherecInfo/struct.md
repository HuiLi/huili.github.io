# 主要结构体
在where.c中，有两个比较重要的结构体:<br>
一个是WhereClause结构体，它用于存储sql语句中的整个的where子句。
<img src="whereClause.png"/>
一个是WhereTerm结构体，它用于存储where子句根据op分隔后的各个语句。
<img src="whereTerm.png"/>
它们之间的关系如下：
<img src="structRelation.png"/>
