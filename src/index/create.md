# 索引的创建
Index *sqlite3CreateIndex(

  Parse *pParse,     //关于这个语法解析器的所有信息

  Token *pName1,   //索引名称的第一部分，这部分可能是空的。
  Token *pName2,    //索引名称的第二部分，可能是空的。
  SrcList *pTblName, //使用索引的表的名称

 pParse->pNewTable //这个表的索引，当为0时用

 pParse->pNewTable表示

  ExprList *pList,   //列索引的列表

  int onError,   //关于OE_Abort、OE_Ignore

  OE_Replace或OE_None的参数

  Token *pStart,    //CREATE指令开始这样的声明

  Token *pEnd,       //当出现）时关闭CREATE

  INDEX语句

  int sortOrder,     //当pList==NULL主键的排序顺序

  int ifNotExist   //如果索引已经存在忽略错误
)

    创建一个SQL表的新索引。pName1.pName2是索引的名称，pTblList是表，该表将被编入索引名。两者都会是NULL的主键或建立满足UNIQUE约束的索引。如果PTABLE和pIndex为NULL，则使用pParse ->pNewTable为表建立索引。pParse -> pNewTable是当前正由一个CREATE TABLE语句构成的表。plist中的列的列表来进行索引。如果这是在最新的专栏中添加表目前正在建设一个主键或唯一约束，PLIST为NULL。如果索引创建成功，返回一个指针在新索引结构。这是使用的sqlite3AddPrimaryKey（）来标记索引作为表的主键（Index.autoIndex==2）。

    索引建立的实现过程如下：

（1）查找数据库是否存在，不存在直接返回

（2）检查表。如果索引的名字是不合格的，则检查表是否是一个临时表

（3）找出索引的名称，确保这里没有其他的索引或同样的索引名称

（4）创建一个假的列表来进行模拟这一操作

（5）找出需要存储多少字节的空间，显示指定序列的名称

（6）分配索引结构

（7）检查一下看看我们是否应该将请求的索引列进行将序排列

（8）扫描表中列的索引的名称，加载列指数到列索引结构中。

（9）添加一个测试，以确保相同的列不会被命名在相同的索引上

（10）定义排序序列的名称

（11）创建一个自动索引，这个自动索引作为主键或唯一子句的列定义，或者是后来的主键或唯一子句的列定义。

（12）链接新的索引结构和其它的内存数据库结构，如果db->init.busy为0，那么在硬盘上创建索引。

（13）创建的rootpage索引

（14）收集CREATE INDEX语句的完整文本到zStmt变量中

（15）在sqlite_master索引中添加一个入口

（16）填充这一索引的数据，然后重新解析模式。

（17）退出清空缓存

（18）完成索引的创建
