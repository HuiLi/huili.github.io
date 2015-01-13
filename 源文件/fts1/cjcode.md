# Summary

创建一个搜索索引通常情况如下：

　• 把文本分解成记号。
　　
　• 转换为小写字母。
　　
　• 确定根词。
　　
　• 建立索引。
　　
 默认情况下，SQLite提供了两个基本的分词器，Simple和Porter。它们可以控制字的分开方式。Simple根据空格和标点符号将文本分解成不同的记号。Porter是专为英文使用而设计，它可以将大量的文字扩展化解为基础形式。例如，condolidate，consolidated，和consolidating这一类词语都会被转变成consolid。
 
 遗憾的是，SQLite目前还没有取消停用词。所以常用词，例如，the,of和to仍位于索引内。这会极大地扩充索引的范畴并减缓搜索速度。最简单的解决办法是，在按下确认检索之前手动除去停用词。
 
下面是创建全文索引的代码。 SQLite之所以做到这些，是因为它通过使用FTS扩展建立一个虚拟表。只有文本列位于这个虚拟表以内时，才可以被搜索，并且最后一列用来识别使用的分词器类型。

  　```
　
      CREATE VIRTUAL TABLE example
　　USING FTS3(title TEXT, TOKENIZE SIMPLE)
　　
　　```

 创建表后，使用SELECT， INSERT，UPDATE和DELETE语句询问此表。此处要附加说明的是：没有进一步的索引可以建立在表格上，所以简单的查询将导致对全表的扫描。
 
## 3.1检索

搜索索引由匹配操作完成。查询可以包含多个方面，在这种情况下，只有文字行包含所有条件时才返回。还有支持“OR”的查询，但该查询排除了条件，精确的词组匹配以及前缀检索。
　　

 ```
    SELECT rowid, title FROM example WHERE title MATCH 'tea bag'
　　SELECT rowid, title FROM example WHERE title MATCH 'tea OR bag'
　　SELECT rowid, title FROM example WHERE title MATCH 'tea -bag'
　　SELECT rowid, title FROM example WHERE title MATCH '"tea bag"'
　　SELECT rowid, title FROM example WHERE title MATCH 'tea*'

```
请注意：OR区分大小写，并且在一次查询中只允许一个MATCH操作。

## 3.2创建片段


为了向匹配的搜索结果提供语境，可以使用snippet()功能。这一功能将突出显示搜索结果中任何文字列的关键词。
　　
      SELECT title, snippet(example)
　　FROM example
　　WHERE title
　　MATCH 'Advent'
　　ADVENT|ADVENT
　　Advent|Advent
　　Advent,_Cornwall|Advent,_Cornwall
　　Advent,_West_Virginia|Advent,_West_Virginia
　　Advent:_One-Winged_Angel|Advent:_One-Winged_Angel
　　Advent_(band)|Advent_(band)
　额外的变量可以用来标示置标，置标过去常用于环绕匹配的表达和任何为缩短结果做的省略。这些分别是第二，第三和第四的变量，它们是第一次被用来标示表名。
 
## 3.3非英语的标记

 可以通过ICU(指令控制单元)库提供的一些基本功能实现对非英语文本进行全文检索的愿望。这个库是PHP 6 的一个核心部分，该库通过在intl 扩展的PHP5.3中也可得。这两种情况下的SQLite将有更多的分词器。
 在ICU分词器将根据语言规则和指定的区域识别词语，然后按照界定拆分。这种方法只适用于某些地域。句法如下：
 
```
　　
      CREATE VIRTUAL TABLE example
　　USING FTS3(title TEXT, TOKENIZE icu en_GB)
```

此处的第二个变量由语言，国家和信息变体组成。














