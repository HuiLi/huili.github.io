# exprAnalyze函数
处理Between语句，调用whereClauseInsert函数插入Virtual terms。

    else if( pExpr->op==TK_BETWEEN && pWC->op==TK_AND ){/* 判断操作符类型 */
        ……
        /* 插入虚项到原Between语句后面 */
        idxNew = whereClauseInsert(pWC, pNewExpr,
        TERM_VIRTUAL|TERM_DYNAMIC);
        exprAnalyze(pSrc, pWC, idxNew);
        ……
    }
处理OR语句，调用专用函数exprAnalyzeOrTerm来分析OR相关的优化。

    else if( pExpr->op==TK_OR ){
        assert( pWC->op==TK_AND );
        exprAnalyzeOrTerm(pSrc, pWC, idxTerm);
        pTerm = &pWC->a[idxTerm];
    }

处理LIKE语句，调用whereClauseInsert函数插入Virtual terms。

    if( !db->mallocFailed ){
        u8 c, *pC;
        pC = (u8*)&pStr2->u.zToken[sqlite3Strlen30(pStr2->u.zToken)-1];
        c = *pC;
        if( noCase ){
            if( c=='A'-1 ) isComplete = 0;
            c = sqlite3UpperToLower[c];
        }
        *pC = c + 1;
    }
    ……
    /* 插入虚项 */
    idxNew1 = whereClauseInsert(pWC, pNewExpr1,TERM_VIRTUAL|TERM_DYNAMIC);




