#  Build.c实现的功能
Build.c属于代码生成器部分，处理以下语法：
<br>
CREATE TABLE<br>
DROP TABLE<br>
CREATE INDEX<br>
DROP INDEX<br>

creating ID lists<br>
BEGIN TRANSACTION<br>
COMMIT<br>
ROLLBACK<br>
（1）表的创建<br>
（2）表的删除<br>
（3）索引的创建<br>
（4）索引的删除<br>
（5）视图的创建<br>
（6）ID序列的创建<br>
（7）ID序列的删除<br>
（8）事务的开始<br>
（9）事务的提交<br>
（10）事务的回滚<br>
用到的函数有如下所示：

Sqlite3BeginParse

TableLock

Sqlite3NestedParse

Sqlite3LocateTable

freeIndex

Sqlite3ResetOneSchemal

Sqlite3StartTable

Sqlite3DeleteTable

Sqlite3AddColumn

Sqlite3AddCollateType

Sqlite3CreateView

Sqlite3ViewGetColumnNames

sqliteViewResetAll

Sqlite3RootPageMoved

destroyRootPage

Sqlite3CodeDropTable

Sqlite3DropTable

Sqlite3CreateForeignkey

Sqlite3DeferForeignkey

Sqlite3RefillIndex

Sqlite3CreateIndex

Sqlite3DropIndex

sqlite3IdListApped

Sqlite3IdListDelete

Sqlite3IdListIndex

Sqlite3BeginTransaction

Sqlite3CommitTransaction

Sqlite3RollbackTransaction

Sqlite3Savepiont

reindexTable

reindexDatabases

Sqlite3Reindex


