# 分词器：FTS tokenizer分析
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个FTS tokenizer 是一组规则从文档或提取字词基本FTS 全文查询的，除非特定的tokenizer 是否创建CREATE VIRTUAL TABLE 语句用于创建FTS 表中, 使用默认的tokenizer , "simple ", 为简单tokenizer 中提取令牌从文档或基本FTS 全文查询可根据以下规则:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;是一个连续字符序列的合格的字词, 字符其中合格的字符是所有字母数字字符"_"字符和所有字符在UTF codepoints 大于或等于128所有其他字符当拆分一个文档的术语他们贡献包相邻的条款.
所有大写字符(UTF ASCII 范围内的codepoints 小于128) , 转换到它们的小写命令对照表作为标记化过程的一部分因此, 不区分大小写的全文查询当使用简单的tokenizer
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;例如, 当文档包含所需文本"right 现在, 他们非常frustrated从文档", 则将提取字词并添加到全文索引顺序, "right , 现在可以将答复非常frustrated "这样的文档也许会全文查询, 如"MATCH ''frustrated '' "作为简单tokenizer 转换在查询中的术语以小写在搜索全文索引.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tokenizer 以及"simple "时, 该FTS 一个tokenizer , 它使用源代码的功能提取听懂算法此tokenizer 使用相同的规则对独立的输入文档到术语但以及folding 全局条目(小写)它使用提取听懂英语语言算法, 以减少日文到一个共同的根例如, 使用相同的输入文档.当在段落的上方.tokenizer 听懂抽取以下标记: "right 现在thei veri frustrat "即使这些条款不能甚至英文单词在某些情况下使用它们以更有用的全文索引比简单tokenizer 更多intelligible 输出序列使用tokenizer 听懂了单据中的未仅匹配全文查询, 例如, "MATCH ''frustrated '' "还查询如"MATCH ''frustration '' "为"frustration "一词减少了此语言的词干分析器这听懂算法为"frustrat "- 就像"frustrated "因此, 当使用tokenizer 听懂, FTS 将被替换为精确匹配术语中, 但对类似的英语语言匹配术语有关该语言的词干分析器这听懂算法,
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;例子, requireddate "simple "和"porter "tokenizers :
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用简单-- 创建一个表, tokenizer插入文档中
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CREATE VIRTUAL TABLE 简单的USING fts3(tokenize=simple) ;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;INSERT INTO 简单的VALUES( ''Right 现在定义'' 非常frustrated '') ;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- 第一个中存储的下面两个查询匹配的文档
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-- 表"simple "第二个不

    SELECT *FROM 简单的WHERE 简单的MATCH ''frustrated '' ;
    SELECT *FROM 简单的WHERE 简单的MATCH ''frustration '' ;
    使用tokenizer 听懂-- 创建一个表插入相同的文档到它
    CREATE VIRTUAL TABLE USING 听懂fts3(tokenize=porter) ;
    INSERT INTO VALUES( 听懂''Right 现在定义'' 非常frustrated '') ;
    -- 下列两个查询与文档存储在表中"porter "
    SELECT *FROM WHERE 听懂MATCH 听懂''frustrated '' ;
    SELECT *FROM WHERE 听懂MATCH 听懂''frustration '' ;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果此扩展是使用SQLITE_ENABLE_ICU 服务管理符号定义一个名为"icu "的内置tokenizer 存在, 则使用ICU 中实现如果第一个参数传递给xcreate( )方法(请参阅fts3_tokenizer此tokenizer h)的可能是一个ICU 区域设置标识符例如"tr_TR "对于土耳其语土耳其中, 或"en_AU "中使用的英语澳大利亚例如:


     CREATE VIRTUAL TABLE thai_text USING fts3(text , tokenize=icu th_TH)


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在ICU tokenizer 实现是很简单的它根据ICU 输入文本进行查找word 边界并放弃所有令牌的规则由网站链接该可能适合于某些应用程序在一些区域, 但不是所有如果更复杂的处理是必需的例如, 可实现提取或放弃标点该都可以通过创建tokenizer 实现使用ICU tokenizer 作为其实现的一部分 .

