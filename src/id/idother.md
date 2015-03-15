# ID序列其他操作
SrcList *sqlite3SrcListEnlarge(

	sqlite3 *db,      //数据库连接通知OOM错误。
	SrcList *pSrc,    //该SrcList待放大。
	int nExtra,     //新的slots数量添加到pSrc- > a[ ]中。
	int iStart      //在第一个slot中的pSrc->a[]设置索引
	)
 工作流如下：

（1）通过在iStart创建nExtra 开始，扩大所给SrcList object空间。

（2）检查在调用参数

（3）如果需要，分配额外的空间

（4）将新插入slots代替现有的slots。

（5）将新分配的slots置为0

（6）返回一个指向放大SrcList的指针。

void sqlite3SrcListAssignCursors(Parse *pParse, SrcList *pList){
在SrcList中所有表分配VdbeCursor索引号，建立分配指向。
void sqlite3SrcListDelete(sqlite3 *db, SrcList *pList)删除整个SrcList包括其所有子序列，释放数据库链接，删除查找语句，删除表达式，释放列的存储空间。
void sqlite3SrcListIndexedBy(Parse *pParse, SrcList *p, Token *pIndexedBy)对SrcList的索引添加条件语句。
void sqlite3SrcListShiftJoinType(SrcList *p)处理语句序列的链接操作。
