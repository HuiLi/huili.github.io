# analyzeOneTable函数
真正实现分析一个单一表所关联的所有索引。

5.1函数原型

该函数的原型如下：

static void analyzeOneTable(
  Parse *pParse,   /* 解析器上下文 */

  Table *pTab,     /* 要分析的表*/

  Index *pOnlyIdx, /*如果为空, 只分析这一个索引*/

  int iStatCur,    /*

  VdbeCursor的索引用于写sqlite_stat1 表 */

  int iMem         /*从次可用的内存 */

)

5.2函数中用到的变量释

该函数中主要用到的一些变量的注释：

sqlite3 *db = pParse->db;    /*数据库句柄 */

  Index *pIdx;                 /*
  一个正在被分析的索引*/

  int iIdxCur;                 /* 打开的正在被分析的索引的下标*/

  Vdbe *v;                     /*建立的虚拟机 */

  int i;                       /*循环计数 */

  int topOfLoop;               /* 循环的开始 */

  int endOfLoop;               /* 循环的结束 */

  int jZeroRows = -1;          /* 如果组数为0从此跳转*/

  int iDb;                     /* 数据库包含的表的索引*/

  int regTabname = iMem++;     /* 记录器包含的表名 */

  int regIdxname = iMem++;     /* 记录器包含的索引名 */

  int regStat1 = iMem++;       /* sqlite_stat1表的stat列*/

  int regCol = iMem++;         /* 被分析的表中一列的内容 */

  int regRec = iMem++;         /* 记录器持有的完全记录 */

  int regTemp = iMem++;        /* 临时用到的记录器*/

  int regNewRowid = iMem++;    /* 插入记录的rowid*/

函数中有一个分析的循环体，这个循环体从索引b-tree的头运行到尾逐一处理。

最后将分析的结果存储在sqlite_stat1表中。如果表中没有索引则创建一个空的sqlite_stat1表。
