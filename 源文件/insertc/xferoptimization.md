# xferOptimization()
输入参数：Parse *pParse, Table *pDest, Select *pSelect, int onError, int iDbDest
返回值：static int
函数作用：函数的前置声明，优化查询（insert into tab select xxx from tab2形式的语句）
解释：传入参数依次为：解析器环境，要插入的表，SELECT语句数据源，处理约束的错误，Pdest数据库，主要是针对数据表的数据进行处理。
