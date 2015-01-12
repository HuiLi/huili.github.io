# 删除表名并重命名为新表名
8.1函数头

void sqlite3AlterRenameTable(
  Parse *pParse,            /* 语法分析器上下文*/
  SrcList *pSrc,            /* 重命名的表*/
  Token *pName              /* 新表名*/
)

8.2工作流程

（1）如果db->mallocFailed从不会发生，就退出重命名表。判断pSrc->nSrc==1和当前线程与数据库连接的互斥量是否保持

（2）判断指针pTab是否存在，如果存在，则赋值数据库iDb和数据库名zDb，反之退出；

（3）检查一个表或索引名zName是否存在数据库中，如是不存在，返回错误。

（4）确保SQLITE_OK不是一个正在被修改的系统表或者一个正在被重命名的保留的名称表，否则退出重命名表

（5）判断pTab->pSelect，sqlite3AuthCheck授权调用，sqlite3ViewGetColumnNames表列名是否存在，如果存在则退出重命名表。

（6）为iDb数据库开始一个事务和代码的VerifyCookie，然后修改模式cookie。
如果表是一个虚拟表打开一个声明事务。

（7）如果pVTab是一个虚拟表,调用xRename()函数。 xRename()回调将修改使用v-table实现的任何资源的名字(包括其他SQLite表)， 这个v-table实现确认虚拟表的名称。

（8）如果启用了外键的支持,重写CREATE TABLE语句对应的所有子表的外键约束重命名表的父表。修改sqlite_master表来使用新的表名。

（9）	如果sqlite_sequence表存在于这个数据库中,那么用新表的名称更新以前的表名
