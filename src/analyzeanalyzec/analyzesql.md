# Analyze的SQL语法
sql-statement ::= 	ANALYZE
sql-statement ::= 	ANALYZE database-name
sql-statement ::= 	ANALYZE [database-name .] table-name

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ANALYZE命令集合关于索引的统计信息并将它们储存在数据库的一个特殊表中，查询优化器可以用该表来制作更好的索引选择。 若不给出参数，所有附加数据库中的所有索引被分析。若参数给出数据库名，该数据库中的所有索引被分析。若给出表名 作参数，则只有关联该表的索引被分析。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最初的实现将所有的统计信息储存在一个名叫sqlite_stat1的表中。未来的加强版本中可能会创建名字类似的其它表， 只是把"1"改为其它数字。sqlite_stat1表不能够被撤销，但其中的所有内容可以被删除，这是与撤销该表等效的行为。
