# sqlite3Update函数结构
函数结构

    void sqlite3Update(
         Parse *pParse,         /* 解析上下文 */
         SrcList *pTabList,     /*  所要改变的表 */
         ExprList *pChanges,    /* 改变的表 */
         Expr *pWhere,          /*  判断是否为空 */
         int onError            /* 错误的处理 */
    )
