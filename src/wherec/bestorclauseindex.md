# bestOrClauseIndex函数
此程序用于找到一个扫描策略，这个策略可以用来优化一个WHERE子句中的 “OR”表达式。与子句FROM中的pScr项相关的表可能是个普通的B树表或者是一个虚表。

    static void bestOrClauseIndex(
        Parse *pParse,              /* 词法分析器上下文 */
        WhereClause *pWC,           /* WHERE子句*/
        struct SrcList_item *pSrc,  /* 用于搜索的FROM子句项*/
        Bitmask notReady,           /* 不能用来索引的游标掩码*/
        Bitmask notValid,           /* 不可用的光标*/
        ExprList *pOrderBy,         /* ORDER BY子句*/
        WhereCost *pCost            /* 最小化查询计划的代价*/
    )

