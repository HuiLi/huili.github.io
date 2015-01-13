# sqlite3Fts3ExprParse()函数

    int sqlite3Fts3ExprParse(sqlite3_tokenizer *pTokenizer,int iLangid,char **azCol,int bFts4, int nCol,int DefaultCol,const char *z, int n, Fts3Expr **ppExpr)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fts3表达式分析函  数，这个函数尝试解析一个查询表达式和创建一个表示已被查询的表达式的Fts3Expr结构体树，参数z和n分别包含  一个指针和一个包含一个fts3查询表达式的缓冲区的长度，如果参数n为负数，则假设z指向一个空终止字符串并且  长度使用strlen()。第一个参数pTokenizer传递了当解析表达式时使用正规查询符号的fts3编译器模块。azCol[]数组，它是被假定为包含nCol入口，并应该包含目标fts3表  格每一列的名字，为了能从左到右表示，列名必须为没有终止符的字符串。iDefaultCol参数应该传递一个出现在MATCH操作的左部表中的列索引(默认match列反对一个表示列的名字没有明确表示它是查询字符串的一部分的符号)，或者如果符号可能是由默认match的表的列则iDefaultCol为-1。

    if( rc==SQLITE_OK && sParse.nNest ){
    rc = SQLITE_ERROR;
    sqlite3Fts3ExprFree(*ppExpr);
    *ppExpr = 0;
    }

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用来检查匹配错误的括号，最后，如果分析成功*ppExpr指向查询表达树的头结点并返回SQLITE_OK，如果有错误发生，返回SQLITE_NOMEN或SQLITE_ERROR，*ppExpr设为空指针。

    void sqlite3Fts3ExprFree(Fts3Expr *p)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;释放一个被sqlite3Fts3ExprParse()分配的已分析的fts3查询表达式。
剩余的代码部分为测试代码，首先是一个查询分词器的哈希  表的函数，然后又实现了一个使用来测试表达式分析器的标量SQL函数。
