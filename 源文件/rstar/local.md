#局部优化
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对M+1的记录进行排序,可以得到M-2m+2种分配方法,根据每种方法结果计算出3个值.
根据得到的值来决定依哪个轴进行分裂,或者直接决定最终分组方案.
对应3个值为:

     area-value=area[G1]+area[G2]
     margin-value=margin[G1]+margin[G2]
     overlap-value=area[G1]∩area[G2]

##相关函数
函数splitNodeStartree用于分裂结点

    static int splitNodeStartree(
      Rtree *pRtree,
      RtreeCell *aCell,
      int nCell,
      RtreeNode *pLeft,
      RtreeNode *pRight,
      RtreeCell *pBboxLeft,
      RtreeCell *pBboxRight
    )
函数SortByDimension用于根据维度对记录进行排序

    static void SortByDimension(
      Rtree *pRtree,
      int *aIdx,
      int nIdx,
      int iDim,
      RtreeCell *aCell,
      int *aSpare
    )
其中,aIdx表示排序记录的下标记录集,nIdx表示记录下标,iDim表示维度数,aSpare表示临时排序空间.
##相关流程图
<img src="images/local.png">
