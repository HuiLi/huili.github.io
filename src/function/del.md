# 表的删除
在删除表之前首先要运行以下函数

destroyRootPage(

Parse *pParse,//建立解析器

int iTable, //表索引

int iDb      //数据库索引

)

删除根页码的表，更新主表和内部模式。
static void sqlite3ClearStatTables(

  Parse *pParse,      //解析上下文

  int iDb,         //创建数据的个数

  const char *zType,  //指向索引或表

  const char *zName //指向索引或表的的命名空间

)

运行删除索引或删除表的指令后从sqlite_statN表删除条目 。运行过以上两个函数之后开始运行函数。

void sqlite3CodeDropTable(Parse *pParse, Table *pTab, int iDb, int isView){

   Vdbe *v;

  sqlite3 *db = pParse->db;

  Trigger *pTrigger;

  Db *pDb = &db->aDb[iDb];

}

生成删除表的指令。在这个函数中调用构建触发器列表，删除触发器，删除某个名称的表序列等方法，做好删除表的前期工作。紧接着运行函数

void sqlite3DropTable(Parse *pParse, SrcList *pName, int isView, int noErr)
{
  Table *pTab;        //要删除的表

  Vdbe *v;           //虚拟数据库引擎

  sqlite3 *db = pParse->db;

  int iDb;

}

删除数据表。
