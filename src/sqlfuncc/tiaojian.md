# 条件判断函数
3.1 static void nullifFunc(  sqlite3_context *context, int NotUsed,  sqlite3_value **argv)

如果函数参数相同，返回NULL，否则返回第一个参数。
与nullif类似的还有ifnull()，它返回第一个非空参数的副本。若两个参数均为NULL，返回NULL。
coalesce()与ifnull()类似。
