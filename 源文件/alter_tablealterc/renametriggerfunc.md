# 对触发器的表名做修改
3.1 函数头

static void renameTriggerFunc( sqlite3_context *context, int NotUsed,  sqlite3_value **argv)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个函数使用SQL语句执行ALTER TABLE命令生成。第一个参数是创建触发器语句的文本。第二个是表名。创建触发器语句中的表名被替换为第三个参数,返回的结果。

3.2 工作流程

（1）	判断zSql是否存在，若存在，循环判断变量dist不为2 或token!=TK_WHEN && token!=TK_FOR && token!=TK_BEGIN)，

（2）	若指针zCsr是否存在，找到一个开括号之前，停止查找

（3）	存储指针zCsr指向表名tname的位置

（4）	推进指针zCsr到下一个标志，如果token==TK_SPACE时，zCsr指针移动加之前的长度。

（5）	替换旧的触发器语句的表名，返回触发器语句的新表名。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这类似上面的renameTableFunc（）函数,除了创建触发器,不用创建索引和创建表。
