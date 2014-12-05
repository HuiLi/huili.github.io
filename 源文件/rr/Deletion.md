#删除
##相关函数
函数DeleteCell用于删除记录

    static int deleteCell(
      Rtree *pRtree,
      RtreeNode *pNode,
      int iCell,
      int iHeight
    )
函数nodeDeleteCell被DeleteCell调用用于删除数据

    static void nodeDeleteCell(
      Rtree *pRtree,
      RtreeNode *pNode,
      int iCell
    )
函数fixLeafParent用于父结点被删除的结点,重新分配父结点

    static int fixLeafParent(
      Rtree *pRtree,
      RtreeNode *pLeaf
    )
函数fixBoundingBox用于调整结点边框

    static int fixBoundingBox(
	  Rtree *pRtree,
	  RtreeNode *pNode
	)
函数removeNode用于删除结点

    static int removeNode(
	  Rtree *pRtree,
	  RtreeNode *pNode,
	  int iHeight
	)
##相关流程图
<img src="images/Deletion.png">
