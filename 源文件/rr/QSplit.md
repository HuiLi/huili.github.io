#平方分裂
##相关函数
函数splitNodeGuttman,R树的分裂方法

    static int splitNodeGuttman(
      Rtree *pRtree,
      RtreeCell *aCell,       /* 分裂结点的单元信息 */
      int nCell,              /* 分裂结点单元数 */
      RtreeNode *pLeft,       /* 分裂后的左右单元 */
      RtreeNode *pRight,
      RtreeCell *pBboxLeft,   /* 分裂后的左右结点边框 */
      RtreeCell *pBboxRight
    )
函数QuadraticPickseeds用于为分裂后的左右结点选择第一条记录

    static void QuadraticPickSeeds(
      Rtree *pRtree,
      RtreeCell *aCell,
      int nCell,
      int *piLeftSeed,         /* 返回分裂后左右结点 */
      int *piRightSeed
    )
函数QuadraticPickNext用于为分裂后的左右结点选择下一条记录

    static RtreeCell *QuadraticPickNext(
      Rtree *pRtree,
      RtreeCell *aCell,
      int nCell,
      RtreeCell *pLeftBox,   /* 分裂后的左右结点边框 */
      RtreeCell *pRightBox,
      int *aiUsed            /* 单元使用信息 */
    )
##相关流程图
<img src="images/QSplit.png">