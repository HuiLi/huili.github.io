# sqlite3OpenTableAndIndices()
输入参数： Parse *pParse, Table *pTab, int baseCur,int op
返回值：int
函数作用：此代码将打开一个表的游标并且表的所有索引baseCur参数是表所使用的游标的数量在随后的  游标索引被打开。
解释：主要是打开要操作的数据表及表的所有索引，为后面插入数据做准备。
