#线性分裂
##相关函数
函数SplitNodeGuttman用于分裂结点

    static int splitNodeGuttman(
      Rtree *pRtree,
      RtreeCell *aCell,
      int nCell,
      RtreeNode *pLeft,
      RtreeNode *pRight,
      RtreeCell *pBboxLeft,
      RtreeCell *pBboxRight
    )
函数LinearPickSeeds用于为分裂后的左右结点选择第一条记录

    static void LinearPickSeeds(
      Rtree *pRtree,
      RtreeCell *aCell,
      int nCell,
      int *piLeftSeed,
      int *piRightSeed
    )
函数LinearPickNext用于为分裂后的左右结点选择下一条插入记录

    static RtreeCell *LinearPickNext(
      Rtree *pRtree,
      RtreeCell *aCell,
      int nCell,
      RtreeCell *pLeftBox,
      RtreeCell *pRightBox,
      int *aiUsed
    )
##相关流程图
<img src="images/LinearSplit.png">