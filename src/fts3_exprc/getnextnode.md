# getNextNode()函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;getNextNode这个函数包括5个参数：*pParse，fts3查询分析上下文指针，缓冲器*z和长度n，表达式指针**ppExpr，表示所消耗字节数的*pnConsumed，结构体Fts3Keyword用来存放4个操作，OR，AND,NOT,NEAR.首先

    while( nInput>0 && fts3isspace(*zInput) )

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用于跳过分隔符（打开或关闭括号或一个被引用的字符串）。

    for(ii=0; ii<(int)(sizeof(aKeyword)/sizeof(struct Fts3Keyword));ii++)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;开始处理一个关键词，先找到一个最接近的词，在这个点上很可能是一个关键词，但是要确定的话，下一个字节必须包含另一个分隔符，一个开或关的括号，一个引用字符，或者文件结束符号。用

    if( fts3isspace(cNext) || Next=='"' || cNext=='(' || cNext==')' || cNext==0 )

来判断，如果不是一个关键词（这种情况会在使用者提供了一个相当于甲骨文的字符时发生），则继续，检查到一个打开关闭的括号后，如果词组是一个实例，就把把整个字符串传送到getNextString()	 处理，如果程序流继续往下的话，那一定是一个规则的记号，或者是在输入的末端，使用sqlite3_tokenizer读取规则的记号，在这之前，要先算出此记号是否存在一个显式的列说明符。

    static int opPrecedence(Fts3Expr *p)

返回一个代表操作优先级的整数，当使用新的fts3查询语法时，优先级顺序为：

    NEAR>NOT>AND>OR.

