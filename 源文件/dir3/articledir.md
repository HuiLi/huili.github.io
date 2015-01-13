# ON子句
&nbsp;&nbsp;&nbsp;&nbsp;On子句优先级仅次于from，用于内连接和外连接，写成join on的形式。比如 :
<pre>
     from table1 inner join table2 on table1.name=table2.name;
     from table1 left outer join table2 on table1.name=table2.name;
   执行的顺序是先执行on再执行join（这点很重要，与where不一样）。
</pre>
