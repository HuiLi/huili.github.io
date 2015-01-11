# 其它一些函数的功能
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**1．clearSelect**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数clearSelect的功能是删除所有选择的内容结构但不释放选择结构本身。其具体解释如表3.7所示。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**表3.7  clearSelect的具体解释**
<table>
<tr>
<td>参数</td>
<td>说明</td>
</tr>
<tr>
<td>sqlite3ExprListDelete(db, p->pEList)</td>
<td>删除整个表达式列表</td>
</tr>
<tr>
<td>sqlite3SrcListDelete(db, p->pSrc)</td>
<td>删除整个SrcList,包括所有的子结构</td>
</tr>
<tr>
<td>sqlite3ExprDelete(db, p->pHaving)</td>
<td>递归删除一个表达式树</td>
</tr>
<tr>
<td>sqlite3SelectDelete(db, p->pPrior)</td>
<td>删除给定的选择结构和所有的子结构</td>
</tr>
</table>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**2．sqlite3SelectDelete**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数sqlite3SelectDelete的功能是删除给定的选择结构和所有的子结构。其代码如下所示。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;void sqlite3SelectDelete(sqlite3 *db, Select *p){<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  if( p ){ /*如果结构体指针p指向的地址非空*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    clearSelect(db, p);   /*删除所有选择的内容结构但不释放选择结构本身*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    sqlite3DbFree(db, p); /*空闲内存,可能被关联到一个特定的数据库连接。*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  }<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**3．tableAndColumnIndex**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数tableAndColumnIndex的作用是在pSrc中搜索前面的N个表，从左向右，搜索列中有名为zCol的表。当找到以后，设置* piTab和* piCol表索引和匹配列的列索引，并返回TRUE 。如果没有找到，返回FALSE。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**4．codeDistinct**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数codeDistinct的作用是添加代码，将检查确保N个寄存器开始iMem形成一个鲜明的条目。iTab是一个分类所引，以前见到的N值的组合。如果当前的N值是新的，一个新的条目由iTab产生。如果前N个元素不明显，跳转到addrRepeat，N+1个值从栈中弹出。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**5．selectOpName**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数selectOpName是一个静态的且是只读的字符型指针函数，其主要功能就是返回连接符的名称。其具体执行过程如图3.1所示。
<img src="selectOpName.jpg">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**图3.1 函数selectOpName**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**6．generateColumnNames**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数主要是生成VDBE结果集中列的名字。这些信息被用于在回调中提供azCol[]的值。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**7．selectColumnsFromExprList**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数主要是给定一个表达式列表(真正的形成一个SELECT语句结果集的表达式列表)，能计算出一个拥有这些表达式列表的表的合适列名称。所有列名将是唯一的，只计算列名称。zType、zColl列和其他字段的列都被置零。返回SQLITE_OK成功。如果内存分配发生错误,在*paCol里存储空，在*pnCol里存储0，返回SQLITE_NOMEM。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**8．selectAddColumnTypeAndCollation**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数主要是基于一个SELECT语句，给列列表添加类型和排序信息。列表大概来自于selectColumnNamesFromExprList()。列表只有名字,没有类型或排序。这个程序边遍历并添加类型和排序。这个程序在SELECT语句中要求的所有标识符被解决了。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**9．sqlite3ResultSetOfSelect**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数主要是给定一个SELECT语句,生成一个表结构，来描述那个SELECT的结果集。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**10．sqlite3GetVdbe**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数主要是对给定的解析器上下文获得一个VDBE，如果需要，创建一个新的VDBE。如果发生一个错误，返回空并且在pParse里留下信息。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**11．computeLimitRegisters**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数主要是基于pLimit和pOffset表达式计算SELECT中的iLimit和iOffset字段。nLimit和nOffset持有这些表达式，这些表达式出现在原始的SQL语句中，在LIMIT和OFFSET关键字之后。或者为空如果这些关键词被省略。如果没有限制和/或偏移,然后iLimit和iOffset是负的。这个程序改变了iLimit和 iOffset的值，只有限制或偏移由nLimit和nOffset定义。iLimit和iOffset应该是预设到适当的默认值(通常但不总是-1)之前调用这个程序。只有nLimit>=0或者nOffset>0做限制寄存器得重新定义。这个UNION ALL操作符使用这个属性来迫使相同的限制和偏移暂存器的重用通过多个SELECT语句。
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**12．multiSelectCollSeq**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数主要是返回适当的排序序列，这个排序是针对compound-select语句“p”中的iCol-th列的结果集。如果列没有默认的排序序列，返回NULL。复合选择的排序序列来自选择最左边的排序序列。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**13．multiSelect**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数主要是处理一个真正的两个的并集或交集查询或者两个以上单独的查询。“p”指向最右边的两个查询。左边的查询是p->pPrior.在递归地将调用这个程序的情况下，左边的查询也可以是复合查询。总查询的结果将被写入一个带有参数iParm的eDest类型目标。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**14．selectAddSubqueryTypeInfo**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数用于为from子句的每个子查询增加Column.zType 和Column.zColl 信息到表结构，以代表子查询的结果集。这个表结构代表被selectExpander()创建的结果集，但是这个类型和校正信息在这时是缺省的，因为标识符还未处理。这个程序在标识符解析后调用。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要调用selectAddColumnTypeAndCollation()函数，该函数用于加入类型信息和校正信息到一个基于select语句的列列表，这个列列表可能来自selectColumnNamesFromExprList()，这个列列表只有名字，没有类型和校正信息，该函数就是用来加入类型和校正信息的。但是这个函数要求在select语句中的所有标识符都已被解析才行。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**15．sqlite3SelectAddTypeInfo**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先sqlite3SelectAddTypeInfo()函数，该函数用于将数据类型和排序序列信息添加到在一个SELECT语句的所有from子句的子查询表结构。该函数在name resolution之后使用。参数Parse *pParse和Select *pSelect分别是Parse 结构体和Select 结构体的一个实例，Parse 结构体表示SQL解析器的上下文，而Select 结构体则是为一个select语句生成代码提供所有的准备信息。这两个结构体包含的信息较多，甚至在结构体中还包含有结构体，相对较复杂。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**16．sqlite3SelectPrep**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数建立一个SELECT语句的处理：（递归地在select的所有子查询中执行。）为所有的from子句分配一个vdbe指针和建立临时表。在结果集中扩展通配符"*" 和"TABLE.*" 。匹配表达式的标识符到相应的表。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其调用了两个函数：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3SelectExpand()，用于扩展select及其子查询。
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3ResolveSelectNames()，用于确定select 及其子查询的名称。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**17．resetAccumulator **<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数用于重置聚合累加器，在计算一个聚集函数时，聚合累加器是保持中间结果集的内存单元集。用于生成代码,这些代码将null存储在所有的内存单元中。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;参数AggInfo *pAggInfo是聚合信息，该结构体包含了必要的为有聚合函数的select语句生成代码的信息。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有两个for循环，这两个for循环是该函数的核心，用于将null值存储到内存单元以达到重置的目的。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**18．finalizeAggFunctions**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数为在AggInfo 结构中的每个聚合函数调用OP_AggFinalize操作码。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其中主要为一个for循环，该循环即用来实现该函数的功能。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**19．updateAccumulator**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数为一个基于当前游标位置的聚合查询更新累加器内存单元。其重要信息如下：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3ExprCacheClear()函数用来清除所有的列缓存；
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3ExprCacheAffinityChange()用来记录Affinity的变化；<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3ReleaseTempRange()用于释放缓存。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;代码是用于清空缓存的，填充累加寄存器之前,必须清空列缓存，否则,如果任何所需的列值已经存在于寄存器,sqlite3ExprCode()可以使用OP_SCopy值复制到pC->iMem。但同时这个值也被使用,初始寄存器可能被使用,使存有文本或blob值的缓存无效。不过另一个解决方案是改变OP_SCopy用来复制缓存值到OP_Copy。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**20．explainSimpleCount**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数用于添加一个单一的OP_Explain 结构到VDBE ，用来解释一个简单的count(*)查询。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**21．explainOneSelect**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数用于生成一个可读的select 表达式的解释。其中包括from子句的解释，where子句，groupby 子句，having 子句，orderby 子句，Limit子句及Offset 子句的解释。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果where子句不为空，那么首先调用sqlite3ExplainPrintf()函数来追加where到缩进，sqlite3ExplainExpr()函数用来生成一个可读的where表达式树的解释，最后调用sqlite3ExplainNL()函数来附加上”\n”以结束where子句的解释。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**22．substExpr**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数功能：扫描pexpr表达式。用pEList的 iColumn-th条目的一个副本来更换每一个引用到名为iTable的表的一列。（但保留到 ROWID列的引用不变）。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此例程是扁平化程序的一部分。一个结果集被在一个SELECT的FROM子句出现的条目定义的子查询，比如VDBE光标指派给FROM子句条目是iTable。此例程对pExpr进行必要更改以便于它直接指到子查询的源表，而不是子查询的结果集。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**23．minMaxQuery**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数的功能是分析参数传递的 SELECT语句来看它是否是一个最小值或最大值查询。如果是，则返回WHERE_ORDERBY_MIN或WHERE_ORDERBY_MAX，否则返回0。目前，一个查询被认为是一个最小或最大值查询，如果：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(1) 在From子句中有一个单一对象。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(2) 在结果集中有一个单一表达式，并且它要么最小值，要么最大值，其中x为一个列参考。

