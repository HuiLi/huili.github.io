# getNextString()函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先

    *fts3ReallocOrFree(void *pOrig, int nNew)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此函数功能是为了扩大内存分配，如果内存溢出，则释放之前所分配的。

    static int getNextString

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数顾名思义为取得下一个字符串，主要含4个参数，fts3查询分析上下文的指针，缓冲器指针z和其长度n，它包含了作为fts3查询表达式出现的引用字符串的内容,整个函数尝试了处理整个缓冲输入和创建的包含结果类型为FTSQUERY_PHRASE的Fts3Expr结构体，如果成功，返回SQLITE_OK并且*ppExpr会指向分配好的Fts3Expr结构体，否则，不论是SQLITE_NOMEM（内存溢出错误）还是SQLITE_ERROR（标记化错误）都会返回其中之一并且ppExpr指针设为空。另外为了使sqlite3_free()能够单独访问并释放表达式，最终的Fts3Expr数据结构体都必须以单独分配的形式存储，此模块中有2种方法实现：

    第一种，在块下面，使用分析器指针通过表达式的符号迭代，这种方法使用 fts3ReallocOrFree()                       在2种动态缓冲区里收集数据：
    缓冲区P:指向Fts3Expr结构体，跟随Fts3Expr结构体，跟随Fts3PhraseToken结构体数组
    这种途径只存在于Fts3PhraseToken数组中。
    缓冲区zTemp:包含所有符号的副本
    第二个途径，在块中开始"if( rc==SQLITE_DONE )"的下方，将缓冲区zTemp添加到p中，并填充到* Fts3Expr and Fts3Phrase结构体中。


