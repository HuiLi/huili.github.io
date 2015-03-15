# updateVirtualtable函数结构
    static void updateVirtualTable(
        Parse *pParse,       /* 解析上下文 */
        SrcList *pSrc,       /* 需要修改的虚拟表 */
        Table *pTab,         /* 虚拟表的建立 */
        ExprList *pChanges,  /*处理 UPDATE 语句中的列变化 */
        Expr *pRowidExpr,    /* 定义一个表达式，用来验证 */
        int *aXRef,          /*映射 pTab 和 pChanges */
        Expr *pWhere,        /*UPDATE语句的WHERE子句 */
        int onError          /* 冲突处理策略,有replace,ignore,fail,abort和rollback*/
    );
