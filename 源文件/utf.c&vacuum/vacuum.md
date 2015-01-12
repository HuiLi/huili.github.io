
# 1. VACUUM命令是SQLite的一个扩展功能，模仿PostgreSQL中的相同命令而来。若调用VACUUM带一个表名或索引名， 则将整理该表或索引。

在SQLite 1.0中，VACUUM命令调用gdbm_reorganize()整理后端数据库文件。SQLITE2.0.0中去掉了GDBM后端，VACUUM无效。在2.8.1版中，VACUUM被重新实现。现在索引名或表名被忽略。当数据库中的一个对象(表，索引或触发器)被撤销，会留下空白的空间。它使数据库比需要的大小更大，但能加快插入速度。实时的插入和删除会使得数据库文件结构混乱，减慢对数据库内容访问的速度。VACUUM命令复制主数据库文件到临时数据库并从临时数据库重新载入主数据库，以整理数据库文件。这将除去空白页，使表数据彼此相邻排列，并整理数据库文件结构。不能对附加数据库文件进行以上操作。若当前有活动事务，该命令无法起作用。对于in-memory数据库，该命令无效。
SQLite3.1中，可以通过使用auto-vacuum模式取代VACUUM命令，使用auto_vacuumpragma开启该模式。

VACUUM实现：

<img src="lc.png">


## 2.头文件—sqliteInt.h；vdbeInt.h


2.1头文件
```
#include "sqliteInt.h"
#include "vdbeInt.h"**如果malloc出现错误,则返回NULL和db.mallocFailed设标**记集。
```


2.2分析(1). sqliteInt.h：sqliteInt.h中可规定整型，其中嵌有sqlitelimit.h文件。指明了在表中对于blob和text字节的限制；指明了表、索引、视图、update、group by、select等的限制，最高能超过32676条记录或字段，SQL语句长度、缓存大小、可以附加数据库的多少、数据库文件的最大页数等等。
(2). vdbeInt.h:字节代码在内存中被封装成sqlite3_stmt对象(内部叫做Vdbe，见vdbeInt.h)，Vdbe（或者说statement）包含执行程序所需要的一切。


##  3.完成已准备好的声明



3.1函数头—vacuumFinalize
static int vacuumFinalize
3.2(1).static int vacuumFinalize
①.在事先准备好的一份声明中，如果存在错误,那么* pzErrMsg存储的也是错误消息的文本。


## 4.执行zSql


4.1函数头—execSql
static int execSql
4.2(1).static int execSql
①.在数据库db上执行zSql。返回一个错误代码。


## 5.执行zSql返回到一列


### 5.1函数头— execExecSql

static int execExecSql
### 5.2(1).static int execExecSql

①.在数据库db上执行zSql。另外在同一个数据库中执行此SQL，该语句返回到最底部的一层。

## 6.清理数据库自由空间


### 6.1函数头— sqlite3Vacuum

void sqlite3Vacuum
### 6.2
(1).void sqlite3Vacuum

①.非标准空命令是用来清理数据库的,在自由空间中释放。在PostgreSQL中，已知SQLite用的是的版本1.0x，仿效真空的命令,真空命令将调用gdbm_reorganize()所有的数据库中的表。但2.0.0 SQLite版本不再使用GDBM，所以这个命令成为一个空操作。



## 7.操作码


### 7.1函数头—qlite3RunVacuum


int sqlite3RunVacuum

### 7.2(1).int sqlite3RunVacuum


①.这个例程实现OP_Vacuum VDBE的操作码。

②保存当前数据库标志的值,可以在恢复之前存储。然后设置writable-schema标志,不设置外键约束

③附加的临时数据库是“vacuum_db”。对这个文件同步编译指示可以设置为“关闭”, 因为如果崩溃发生，它将不能恢复.sqlite3BtreeCopyFile()被调用之前的状态，数据库的完整性被在主数据库中打开的(可能是同步的)事务维持。

④.execSql()被调用时，这个临时数据库已经存在文件锁(在阅读模式状态下得出结论时，有多个事务的主动声明在这里解锁,但这并不使以后问题来调用BtreeSetPageSize()。
