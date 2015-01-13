# Delete.c中主要函数的实现及功能
delete.c中的主要函数包括以下几个：<br>
<pre>
1.Table *sqlite3SrcListLookup()
2.int sqlite3IsReadOnly()
3.void sqlite3MaterializeView（）
4.Expr *sqlite3LimitWhere()
5.void sqlite3DeleteFrom
6.void sqlite3GenerateRowDelete()
7.void sqlite3GenerateRowIndexDelete()
8 int sqlite3GenerateIndexKey( )
</pre>
接下来分别介绍这些函数。



