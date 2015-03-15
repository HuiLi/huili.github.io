#整体优化
采用强制重新插入算法使树的结构优化。
##相关函数
函数Reinsert用于强制重插入

    static int Reinsert(
      Rtree *pRtree,
      RtreeNode *pNode,
      RtreeCell *pCell,
      int iHeight
    )
函数SortByDistance用于根据记录与结点边界框的中心距离大小将记录排序

    static void SortByDistance(
      int *aIdx,
      int nIdx,
      RtreeDValue *aDistance,
      int *aSpare
    )
其中,aIdx表示排序记录的下标记录集,nIdx表示记录的下标,aSpare表示临时排序空间。aDistance用于存放每个记录计算后的距离集.
##相关流程图
<img src="images/all.png">
