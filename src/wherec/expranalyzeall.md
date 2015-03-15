# exprAnalyzeAll函数
分析所有的Where语句中的子句对所有WhereClause中的WhereTerm执行exprAnalyze。


    static void exprAnalyzeAll(
        SrcList *pTabList,       /* the FROM clause */
        WhereClause *pWC         /* 待分析的WhereClause */
    ){
        int i;
        for(i=pWC->nTerm-1; i>=0; i--){
            exprAnalyze(pTabList, pWC, i);
        }
    }
