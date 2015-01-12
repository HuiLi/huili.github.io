# bestBtreeIndex函数
为某个特定的表寻找最佳查询计划。确定最好的查询计划和成本，
写入最后一个传入的WhereCost对象参数。

分析影响查询策略代价的因数计算查询开销，因素主要包括：

1. 能否使用索引；
2. 记录数量；
3. 是否排序；
4. 查询条件的类型。


* 如果有INDEX BY 子句 （pSrc->pIndex） 附加到 SQL 语句中，
此函数只考虑使用指定的索引。如果没有这样的计划找到，那么返回的成本就是 SQLITE_BIG_DBL。

* 如果没有索引子句 （pSrc->notIndexed!=0） 附加到的表中的 SELECT 语句，认为没有索引。然而，所选的计划仍然可以利用内置的 rowid 主键索引。


    /* Loop over all indices looking for the best one to use
    遍历其所有索引,找到一个代价最小的索引
    */
    for(; pProbe; pIdx=pProbe=pProbe->pNext){
        const tRowcnt * const aiRowEst = pProbe->aiRowEst;
        double cost;                /* 使用pProbe的代价 */
        double nRow;                /* 预计结果集中的记录数 */
        double log10N = (double)1;  /*十进制汇总 (不精确) */
        int rev;                    /* 逆向正确扫描 */
        int wsFlags = 0;
        Bitmask used = 0;

