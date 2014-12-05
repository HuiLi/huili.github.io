#插入
##相关函数
在SQLite中,插入主要涉及到以下几个函数:
函数ChooseLeaf 用来定位插入记录的叶结点

    static int ChooseLeaf(
    Rtree *pRtree,
    RtreeCell *pCell,            /* 需要定位的记录 */
    int iHeight,                 /* 子树高度*/
    RtreeNode **ppLeaf           /* 返回结点指针 */
    )
函数rtreeInsertCell用来插入记录

    static int rtreeInsertCell(
      Rtree *pRtree,
      RtreeNode *pNode,         /*记录插入的结点*/
      RtreeCell *pCell,         /*待插入的记录*/
      int iHeight
    )
函数nodeInsertCell被rtreeInsertCell调用

    static int
      nodeInsertCell(
      Rtree *pRtree,
      RtreeNode *pNode,
      RtreeCell *pCell
    )
函数AdjustTree用来调整树结构

    static int AdjustTree(
    Rtree *pRtree,
    RtreeNode *pNode,                 /* 需要调整的结点 */
    RtreeCell *pCell                  /* 刚插入的单元*/
    )
##相关流程图
<img src="images/ChooseLeaf.png">
<img src="images/Insertion.png">