# 重命名表的名称
1.1函数头

static void renameTableFunc( sqlite3_context *context, int NotUsed,  sqlite3_value **argv;)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个函数使用SQL语句执行ALTER TABLE命令生成。返回结果为由第三个参数替换创建的表名和创建索引语句后返回修改的表名称，即给表名或索引语句重命名。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数中第一个参数是创建表或创建索引的命令的文本。第二个参数是表名。

1.2  工作流程

（1）	判断zSql是否存在，若存在，循环判断token!=TK_LP && token!=TK_USING，

（2）	若指针zCsr是否存在，找到一个开括号之前，停止查找

（3）	存储指针zCsr指向表名tname的位置

（4）	推进指针zCsr到下一个标志，如果token==TK_SPACE时，zCsr移动加之前的长度。

（5）	替换以前的表名，返回重命名的表名。
如下例：sqlite_rename_table('CREATE TABLE abc(a, b, c)', 'def')

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回结果为'CREATE TABLE def(a, b, c)'。

如图为renameTableFunc函数实现流程
<img src='renameTableFunc函数.jpg'>
