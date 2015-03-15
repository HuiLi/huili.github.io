# Analyze 命令接口
2.1生成ANALYZE命令代码
void sqlite3Analyze(Parse *pParse, Token *pName1, Token *pName2)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;解析器调用这个例程当它识别ANALYZE命令。生成ANALYZE命令代码。

格式1：ANALYZE
格式2：ANALYZE database-name
格式3：ANALYZE [database-name ] table-name
格式1：所有附加数据库中的所有索引被分析。
格式2：若参数给出数据库名，该数据库中的所有索引被分析。
格式3：若给出表名 作参数，则只有关联该表的索引被分析。

2.2 sqlite3Analyze接口的关键代码及工作流程
assert( sqlite3BtreeHoldsAllMutexes(pParse->db) );
  if( SQLITE_OK!=sqlite3ReadSchema(pParse) ){
    return;
  }

（1）读取数据库的模式schema.如果有错误发生,保留一个错误信息

（2）解析器中的代码，并返回空。

第一种情况主要代码：
  if( pName1==0 ){
    for(i=0; i<db->nDb; i++){
if( i==1 ) continue;
analyzeDatabase(pParse, i);
    }
  }

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当第一个参数pName1为0时，此时为格式1，需要分析所有。当i==1时为TEMP数据库不需要分析，直接跳过。其余的调用analyzeDatabase接口完成分析。

第二种情况分支代码：
else if( pName2->n==0 ){
    iDb = sqlite3FindDb(db, pName1);
    if( iDb>=0 ){
      analyzeDatabase(pParse, iDb);
    }else{
      z = sqlite3NameFromToken(db, pName1);
      if( z ){
        if( (pIdx = sqlite3FindIndex(db, z, 0))!=0 ){
          analyzeTable(pParse, pIdx->pTable, pIdx);
        }else if( (pTab = sqlite3LocateTable(pParse, 0, z, 0))!=0 ){
          analyzeTable(pParse, pTab, 0);
        }
        sqlite3DbFree(db, z);
      }
    }
  }

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当第一个参数不为0，而第二个参数为0时，此时符合格式2,需要对给定的数据库进行分析，先调用sqlite3FindDb查找给定的pName1数据库，如果查找成功，则调用analyzeDatabase分析数据库。如果查找不成功，则通过pName给定的数据库分析表。

第三种格式代码：
else{
    iDb = sqlite3TwoPartName(pParse, pName1, pName2, &pTableName);
    if( iDb>=0 ){
      zDb = db->aDb[iDb].zName;
      z = sqlite3NameFromToken(db, pTableName);
      if( z ){
        if( (pIdx = sqlite3FindIndex(db, z, zDb))!=0 ){
          analyzeTable(pParse, pIdx->pTable, pIdx);
        }else if( (pTab = sqlite3LocateTable(pParse, 0, z, zDb))!=0 ){
          analyzeTable(pParse, pTab, 0);
        }
        sqlite3DbFree(db, z);
      }

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;不是前面两种情况即为第三种格式，此时只分析完全符合的表。其中用到analyzeTable接口在后面介绍。过程如下图：
<img src='Analyze 命令分析过程.jpg'>
