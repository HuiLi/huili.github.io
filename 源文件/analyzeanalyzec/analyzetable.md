# analyzeTable函数
4.1函数原型

static void analyzeTable(Parse *pParse, Table *pTab, Index *pOnlyIdx)

参数1：解析上下文，参数2：pTab要分析的表，参数3：该索引是否为是表的唯一索引

函数的功能：对数据库中的一个表进行分析，如果参数pOnlyIdx不为空那么这个表中的唯一索引将被分析。

4.2函数流程

过程如下图所示：
<img src='analyzeTable 调用过程.jpg'>
主要代码如下：

assert( pTab!=0 );
  assert( sqlite3BtreeHoldsAllMutexes(pParse->db) );

  iDb = sqlite3SchemaToIndex(pParse->db, pTab->pSchema);

  sqlite3BeginWriteOperation(pParse, 0, iDb);
  iStatCur = pParse->nTab;

  pParse->nTab += 3;

  if( pOnlyIdx ){
    openStatTable(pParse, iDb, iStatCur, pOnlyIdx->zName, "idx");

  }else{
    openStatTable(pParse, iDb, iStatCur, pTab->zName, "tbl");

  }
  analyzeOneTable(pParse, pTab, pOnlyIdx, iStatCur, pParse->nMem+1);

  loadAnalysis(pParse, iDb);

附加了一些判断，不合法的情况以及互斥锁等等其真正实现分析表的是analyzeOneTable。

最后调用loadAnalysis把最近使用的索引写入hash表中，以便以后方便使用。
