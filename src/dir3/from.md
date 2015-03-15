# FROM子句具体使用
FROM子句中可以包含以下元素：<br>
<br>1.Table_name（表名）
<br>2.alias（别名）
<br>3.嵌套查询的结果集<br>
from子句后加()，子查询的结果会放到一个临时表中，要给子查询的结果集起别名
<br>4.join_type，可以使用inner join和left outer join。右外连接和全连接没有实现。

