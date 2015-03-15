# xferCompatibleIndex()
输入参数：Index *pDest, Index *pSrc
返回值：static int
函数作用：查看索引pSrc是否是可兼容的作为数据源为了索引pDest在一个插入转移最优中。
可兼容的索引规则：
   *   该索引是在相同的列集合中
   *   DESC 和 ASC 相同标记在所有列上
   *   相同的onError加工过程
   *   相同的校准顺序在每一列上
解释：主要做索引的比较，对比是否是相照应的。
