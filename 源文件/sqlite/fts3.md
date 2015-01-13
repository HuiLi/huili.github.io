# FTS3概述
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FTS3是一个SQLite 虚拟表的模块, 允许用户执行全文搜索一组文档从最常见()方法来描述"what google 、yahoo 和altavista 全文搜索才能用world wide web 上的文档"用户的输入一个术语或一系列术语可能是由二元运算符连接或分组为一个短语与全文查询系统和发现这些步骤最匹配的这组文档考虑到指定的操作符和分组用户具有的本文介绍FTS3 的部署和用法。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FTS 表时使用INSERT , UPDATE 和DELETE 语句中的方式与普通SQLite 表只有主表中以及由用户名为"(或"content "列如果没有指定模块参数指定的一部分CREATE VIRTUAL TABLE statement) , 每个FTS 表都有一个"rowid "列一个FTS 表的行为方式相同列的一个普通的SQLite 表除了值存储在一个FTS 表未对id 列使用保持不变如果数据库是重建VACUUM 命令FTS表, "docid "是允许作为别名与往常"rowid ", "oid "和"_oid_ "标识符在试图插入或更新某行在表中已经存在具有一个docid 值.该值是一个错误, 就像它将使用普通的SQLite 表
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"docid 之间有一个其他细微差异", 并且标准SQLite 别名未对id 列通常如果一个INSERT 或UPDATE 语句指定两个或两个以上的离散值的未对id 列的别名SQLite写入最右边的"中指定的类值的INSERT 或UPDATE 语句添加到数据库但是, 将non-NULL 值分配给客户机的"docid "和一个或多个SQLite 别名将在插入或更新一个FTS 表被认为是一个错误请参阅下面的示例

    -- 创建FTS 表
    CREATE VIRTUAL TABLE 页USING fts4(title , body) ;
    插入具有特定行docid -- 值.
    INSERT INTO pages(docid 、标题、主体)VALUES(53,  ''home 页", ''SQLite 是一个software... '') ;
    -- 插入一行并允许FTS 分配一个docid 值使用相同的算法
    -- SQLite 使用16在这种情况下, 将会54 , 新docid
    -- 大于最大的docid 出现在表之间的关系.
    INSERT INTO pages(title , 主体)VALUES( ''Download '',  ''all SQLite 源code... '') ;



