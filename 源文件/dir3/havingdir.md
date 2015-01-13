# Having子句
&nbsp;&nbsp;&nbsp;&nbsp;
HAVING 子句对 GROUP BY 子句设置条件的方式与 WHERE 子句和 SELECT 语句交互的方式类似。WHERE 子句搜索条件在进行分组操作之前应用；而 HAVING 搜索条件在进行分组操作之后应用。HAVING 语法与 WHERE 语法类似，但 HAVING 可以包含聚合函，HAVING 子句可以引用选择列表中出现的任意项。在SQLite中sqlite3Select函数用一个表达式结构体表示having子句（Expr *pHaving）。
<img src="../having1.png"/>
