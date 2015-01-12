# 查询策略代价模型及计算方式
>SQLite中计算一个查询策略的代价主要考虑查询记录的数量、是否排序以及是否需要访问索引和原表。

    struct WhereCost {
        WherePlan plan;  /* 策略计划 */
        double rCost;    /* 该查询计划的总成本bestBtreeIndex函数中计算 */
        Bitmask used;    /* Bitmask of cursors used by         this plan */
    };
查询代价计算方式：
* 无可用索引，则查找是否有查询RowId的条件。


    ①	类似Rowid=EXPR的条件，代价0，记录数1；
    ②	类似RowId IN（），IN括号中为给出列表，估计代价NLogN，N为IN中的元素个数，记录数N；
    ③	类似RowId IN（），IN括号中为子查询，估计代价200，记录数100；
    ④	RowId的范围查询，代价记录数为总记录数三分之一；
    ⑤	全表查询，估计代价记录数乘4；
    ⑥	若SQL语句包含ORDER BY，代价乘NLogN。
*	有可用索引，统计每个表索引信息。


    ①	操作符为=或IN，=操作符没有附加代价。IN操作符中，若是子查询的附加代价为25，给出列表的附加代价为元素数N；
    ②	索引范围查询，记录数除3，估计代价除3；
    ③	若SQL语句包含ORDER BY，代价乘NLogN。
