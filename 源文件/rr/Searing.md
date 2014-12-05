# 查找
在SQLite中,查找不在局限于对于单个记录的查找.一般是由用户提供多种约束条件来查找相关信息.
##1 与查找有关的结构
首先介绍SQLite查找游标的结构

    struct RtreeCursor {
      sqlite3_vtab_cursor base;
      RtreeNode *pNode;           /* 当前所指向结点 */
      int iCell;                  /* 结点单元的下标 */
      int iStrategy;              /* 扫描策略 */
      int nConstraint;            /* 约束条件的数量 */
      RtreeConstraint *aConstraint;     /* 约束信息 */
    }
SQLite R树过滤器结构

    static int rtreeFilter(
      sqlite3_vtab_cursor *pVtabCursor,
      int idxNum, const char *idxStr,
      int argc, sqlite3_value **argv
    )
其中,pVtabCursor即是RtreeCursor结构指针。idxNum,iStrategy是查找策略。当idxNum=1时，对应扫描策略1:根据rowid扫描.当idxNum=2时,对应扫描策略2:全表扫描或者根据约束条件扫描.
##2 与查找有关的函数
函数findLeafNode用于rowid查找

    static int findLeafNode(
      Rtree *pRtree,
      i64 iRowid,
      RtreeNode **ppLeaf
    )
其中,iRowid是与查询记录对应的行id.ppLeaf返回结果即记录所在叶结点的指针.
函数descendToCell用于表扫描及约束扫描

    static int descendToCell(
    Rtree *pRtree,
    RtreeCursor *pCursor,
    int iHeight,
    int *pEof                 //用于判断是否成功结束
    )
其中,iHeight是当前扫描结点的子树高度
##3 查找流程
流程图如下所示:
<img src="images/searching.png">
