# 修改任何外键约束定义重命名表为父表
2.1函数头

static void renameParentFunc( sqlite3_context *context, int NotUsed, sqlite3_value **argv)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个函数通过使用SQL代码生成的ALTER TABLE……RENAME命令来修改表的任何外键约束的定义，使用的表被重命名为父表。它是通过三个参数:

1)CREATE TABLE语句的完整文本被修改,
2)表的旧名称重命名
3)表的新名称重命名。
返回新创建TABLE语句。

2.2 工作流程

（1）判断外键约束条件token==TK_REFERENCES是否存在，如果不存在，释放父表。

（2）如果存在，循环判断token==TK_SPACE，移动指针z，直到令牌标志为non-space

（3）如果父表zParent为空，退出函数；否则引用zParent与旧表比较，替旧表名，输出新表名即父表

（4）释放父表zParent

例如:

sqlite_rename_parent('CREATE TABLE t1(a REFERENCES t2)', 't2', 't3')

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回结果为 'CREATE TABLE t1(a REFERENCES t3)'
