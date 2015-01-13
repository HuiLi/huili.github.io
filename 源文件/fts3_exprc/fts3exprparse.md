# fts3ExprParse()函数

    static int fts3ExprParse( ParseContext *pParse,const char *z, int n,Fts3Expr **ppExpr, int *pnConsumed)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数解析建立在缓冲器z上长度为n的fts3查询表达式，如果成功，则返回SQLITE_OK,*ppExpr指向被解析过的form，pnConsumed 指向，从缓冲区 z读来的字节数，否则，*ppExpr设为空指针，并且返回SQLITE_NOMEM（当内存溢出时）或SQLITE_ERROR（解析错误）。
