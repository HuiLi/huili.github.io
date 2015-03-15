# 视图的创建
void sqlite3CreateView(
	Parse *pParse,     //建立解析器

	Token *pBegin,    // CREATE语句开始的标志
	Token *pName1,    //包含视图的数据库名
	Token *pName2,     //视图的名字
	Select *pSelect,   //将成为视图的select语句
	int isTemp,        //视图是否为临时视图
	int noErr        //如果视图存在，报错误消息
	)
    创建视图的过程首先通过函数sqlite3StartTable建立临时表，再通过函数sqlite3SchemaToIndex(db, p->pSchema)查找数据库索引，再复制全部的SELECT语句,定义视图，定位CREATE VIEW语句最后，确保sEnd指向最后。

接着使用sqlite3EndTable()添加到视图SQLITE_MASTER表，通过函数sqlite3ViewGetColumnNames(Parse *pParse, Table *pTable){

Table *pSelTab;   //视图查询结果的临时存储表

Select *pSel;    //实现视图的副本

	int nErr = 0;     //遇到的错误数量】

int n;           / /暂时持有游标的数量分配

sqlite3 *db = pParse->db;
//内存分配中数据库连接错误

int(*xAuth)(void*, int, const char*, const char*, const char*, const char*);

}

得到视图的列名字。
接着使用函数sqliteViewResetAll(sqlite3 *db, int idx) 清除数据库中视图的列名称。
