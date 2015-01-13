#  getNextToken()函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;getNextToken（）函数包含6个参数，分别检索全文指针*pParse，Fts3Phrase. iColumn的值iCol，指向输入字符串的指针z和长度n，字节消耗数*pnConsumed，输出表达式：*pnConsumed，这个函数主要是从正在使用分词器的缓冲器z中提取下一个标记和其他语法分析的信息，**ppExpr这个参数在缓冲器找到标记之前指针也指到结尾的话要设为空，这是为了让其进入sqlite3_free()从而完全释放分配的Fts3Expr结构，如果函数成功得到信息则返回SQLITE_OK，或者如果内存分配失败返回SQLITE_NOMEN。
