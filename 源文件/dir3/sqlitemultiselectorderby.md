# SQLite中multiSelectOrderBy函数
multiSelectOrderBy函数：后跟一个order by表达式列表，将列表中属性迭代出，才进行排序处理。
<pre>static int multiSelectOrderBy(
	  Parse *pParse,       /*解析上下文*/
	  Select *p,            /*编写最右边的SELECT*/
	  SelectDest *pDest    /*处理结果集结构体*/
	)
</pre>
