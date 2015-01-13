# FTS3
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FTS3(full-text search)模块主要实现了全文搜索，它允许用户创建内置全文索引的虚表，全文索引有助于在数据库中高效的查找文字，它是建立在SQLite的核心之中，FTS3是SQlite全文检索的第二次扩展，FTS3实现全文检索的工作原理如图1所示:

<img src="fts3.jpg">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当存储一条文档数据时，解析器与分词器将该文档数据划分成各自独立的词项，并为每个词项建立一个倒排索引。当查询时，解析器与分词器将查询数据进行词项划分，然后遍历倒排索引，找到其相应的记录号，最后根据与查询条件的相关性等排序规则，返回结果集。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
