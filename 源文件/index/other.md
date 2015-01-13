# 与索引相关的其他操作
Index *sqlite3FindIndex(sqlite3 *db, const char *zName, const char *zDb)给出这个特殊的索引的名字和数据库的名称，这个数据库包含这个索引。工作流如下：

（1）给出这个特殊的索引的名字和数据库的名称，这个数据库包含这个索引。

（2）主函数前搜索TEMP

(3)Hash查找(&pSchema->idxHash, zName, nName)

void sqlite3UnlinkAndDeleteIndex(sqlite3 *db, int iDb, const char *zIdxName)对于所谓的zIdxName索引是数据库中IDB中发现的，不同于其表的索引，然后从索引哈希表和索引相关的所有自由内存结构中删除索引。
工作流如下：

（1）Len获取zIdxName前30个字符

（2）论证ALWAYS()函数

（3）回收被索引使用的内存

     static void sqlite3RefillIndex(Parse *pParse, Index *pIndex, int memRootPage)生成一个将擦除和重新填充索引 PIDX代码。这是用于初始化一个新创建的索引或重新计算索引的响应REINDEX命令的内容。如果memRootPage不为负，则意味着该索引是新创建的。由emRootPage指定的寄存器包含索引的根页码。如果memRootPage是负的，则该索引已经存在，并且被重新填充之前必须被清除，而指数的根页号码是Index - > tnum。
工作流如下：

（1）登记所得到的索引的记录,连接数据库

（2）请求一个写锁在这个表上执行这些操作

（3）如果要使用游标打开游标分选机

（4）打开表，遍历表的所有行，将索引记录插入到分选机

（5）通过OP_IsUnique用Sqlite3GetTempRange()分配的操作码的内部方法Sqlite3GenerateIndexKey()方法的调用访问寄存器。

（6）为SQL表创建新的索引
