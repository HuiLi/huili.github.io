
# sqlite查询处理的关键函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sqlite的查询处理首先进行语法分析，语法分析最终在sqlite3SelectNew中完成；然后生成执行计划，select的执行计划在sqlite3Select中完成，该函数先对SQL语句进行语义分析，然后再进行优化，最后生成执行计划。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;语法分析的主要任务就是对用户输入的SQL语句进行语法检查，然后生成一个包含所有信息的语法树。对于SELECT语句，这个语法树最终由结构体Select表示，结构体select的具体说明如表3.1所示。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**表3.1  结构体select的具体说明**
<table>
<tr>
<td>参数</td>
<td>说明</td>
</tr>
<tr>
<td>*pEList</td>
<td>输出结果列的语法树</td>
</tr>
<tr>
<td>*pSrc</td>
<td>from子句语法树</td>
</tr>
<tr>
<td>*pWhere</td>
<td>where语句的语法树</td>
</tr>
<tr>
<td>*pGroupBy</td>
<td>group by语句的语法树</td>
</tr>
<tr>
<td>*pHaving</td>
<td>having语句的语法树</td>
</tr>
<tr>
<td>*pOrderBy</td>
<td>order by语句的语法树</td>
</tr>
<tr>
<td>*pPrior</td>
<td>在复合select语句之前选择</td>
</tr>
<tr>
<td>*pNext</td>
<td>复合语句左边的下一个select</td>
</tr>
<tr>
<td>*pRightmost</td>
<td>在复合select语句中最右边的select</td>
</tr>
<tr>
<td>*pLimit</td>
<td>LIMIT 表达式.，NULL 意味着未使用</td>
</tr>
<tr>
<td>*pOffset</td>
<td>OFFSET 表达式，NULL 意味着未使用</td>
</tr>
<tr>
<td>iLimit, iOffset</td>
<td>内存寄存器持有 LIMIT & OFFSET 计数器</td>
</tr>
<tr>
<td>addrOpenEphm</td>
<td>与select相关的操作码OP_OpenEphem</td>
</tr>
</table>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Select结构体需要注意几个字段，pEList输出结果列的语法树；pSrc为FROM子句语法树；pWhere为WHERE部分的语法树。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**1．sqlite3SelectNew**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Select语法分析在最终在sqlite3SelectNew中完成。sqlite3SelectNew主要就是将之前得到的各个子语法树汇总到Select结构体，并根据该结构，进行接下来语义分析及生成执行计划等工作。sqlite3SelectNew的具体解释如表3.2所示。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**表3.2  sqlite3SelectNew的参数及说明**
<table>
<tr>
<td>参数</td>
<td>说明</td>
</tr>
<tr>
<td>sqlite3 *db = pParse->db</td>
<td>结构体Parse的成员db赋值给结构体sqlite3指针db</td>
</tr>
<tr>
<td>sqlite3DbMallocZero(db, sizeof(*pNew) )</td>
<td>分配和零内存，如果分配失败，使mallocFaied标志在连接指针中</td>
</tr>
<tr>
<td>assert( db->mallocFailed || !pOffset || pLimit )</td>
<td>判断分配是否失败或pOffset值为空或
pLimit值不为空</td>
</tr>
<tr>
<td>sqlite3ExprListAppend</td>
<td>新添加的元素在表达式列表的末尾。新添加元素的地址赋给pEList。如果pList的初始数据为空，那么新建一个新的表达式列表。如果出现内存分配错误，则整个列表被释放并返回空。如果返回的是非空，则保证新的条目成功追加。</td>
</tr>
<tr>
<td>clearSelect(db, pNew)</td>
<td>删除所有选择的内容结构但不释放选择结构本身</td>
</tr>
</table>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;语义分析完之后生成执行计划（语法树到OPCODE），Select的执行计划在sqlite3Select中完成。其实，sqlite3Select函数先对SQL语句进行语义分析，然后再进行优化，最后生成执行计划。对于SQL语句，生成的执行计划(虚拟机opcode)大致分成5部分，前4部分都在sqlite3Select()中生成，它主要调用了以下几个函数，如图3.1所示。
<img src="sqlite3select.jpg">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其中第一部分和第二部分在sqlite3WhereBegin()中生成，第二部分即所谓的查询优化处理；第三部分在 selectInnerLoop中生成；第四部分在sqlite3WhereEnd中生成；第五部分是在sqlite3FinishCoding中完成的。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;生成代码的过程是：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先获得一个vdbe，然后为from语句中的子查询生成代码，然后分别对子查询中的聚合函数，groupby，orderby，distinct等进行相应的处理，带有这些关键字与不带的处理过程有很大的不同，例如，对于groupby子句，如果有groupby子句，那么可能就需要一个分类索引来实现，因此要为其分配分类索引。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**2．sqlite3Select**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数为参数中给出的select语句生成代码，结果的不同取决于SelectDest 结构指向的内容，给出的参数如表3.3所示。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**表3.3  sqlite3Select的参数及说明**
<table>
<tr>
<td>参数</td>
<td>说明</td>
</tr>
<tr>
<td>pDest->eDes</td>
<td>Result</td>
</tr>
<tr>
<td>SRT_Output</td>
<td>为结果集中的每一行生成一个输出行(用OP_ResultRow  操作码)</td>
</tr>
<tr>
<td>SRT_Mem</td>
<td>仅在结果只有一列时有效在寄存器pDest->iSDParm存储第一个结果行的第一列，然后抛弃剩余的查询其目的蕴含着"LIMIT 1"</td>
</tr>
<tr>
<td>SRT_Set</td>
<td>结果必须是一列，在表pDest->iSDParm中存储结果的每一行，并作为表中的键在存储结果前申请pDest->affSdst 用来实现"IN (SELECT ...)"</td>
</tr>
<tr>
<td>SRT_Union</td>
<td>在被pDest->iSDParm 鉴定了的临时表中以键值存储结果</td>
</tr>
<tr>
<td>SRT_Except</td>
<td>从临时表pDest->iSDParm  中移除结果</td>
</tr>
<tr>
<td>SRT_Table</td>
<td>在临时表pDest->iSDParm  中存储结果这就像SRT_EphemTab  被排除，表假设已经开放</td>
</tr>
<tr>
<td>SRT_EphemTab</td>
<td>生成一个临时表pDest->iSDParm  并将结果存储在其中</td>
</tr>
<tr>
<td>SRT_Coroutine</td>
<td>每次调用都生成一个返回一个新行的结果co-routine。co-routine的入口点存储在寄存器pd - > iSDParm </td>
</tr>
<tr>
<td>SRT_Exists</td>
<td>如果结果集是空的，那么在存储单元pDest->iSDParm 中存储一个1   </td>
</tr>
<tr>
<td>SRT_Discard</td>
<td>丢弃结果</td>
</tr>
</table>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（1）该函数还用于返回错误数，如果有错误的话，那么一个恰当的错误信息就被留在pParse->zErrMsg中。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（2）该函数没有释放进入的select 结构，而回叫函数则需要释放select 结构。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（3）该函数中声明了很多变量，这些变量中有数据库的连接，与select语句有关的聚合信息，如order by子句，group by子句等。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**3．sqlite3WhereBegin**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;pTabList是由分析器对FROM部分生成的语法树，它包含FROM中表的信息；pWhere是WHERE部分的语法树，它包含WHERE中所有表达式的信息；ppOrderBy 对应ORDER BY子句(暂不考虑)。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sqlite的查询优化做得简单又精致。在一个简单的sqlite3WhereBegin函数中，完成所有的优化处理。查询优化的基本理念就是嵌套循环(nested loop)，select语句的FROM子句的每个表对应一层循环(INSERT和UPDATE对应只有一个表SELECT语句)。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;pTabList 用于扫描所有的表，有三种基本的扫描策略：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(1)全表扫描，这种情况通常出现在没有WHERE子句时；
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(2)基于索引扫描，这种情况通常出现在表有索引，而且WHERE中的表达式又能够使用该索引的情况；<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(3)基本rowid的扫描，这种情况通常出现在WHERE表达式中含有rowid的条件。该情况实际上也是对表进行的扫描。可以说，Sqlite以rowid为聚簇索引。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数sqlite3WhereBegin的具体参数如表3.4所示。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**表3.4  sqlite3WhereBegin的具体参数**<br>
<table>
<tr>
<td>参数</td>
<td>说明</td>
</tr>
<tr>
<td>*pParse</td>
<td>语义分析</td>
</tr>
<tr>
<td>*pTabList</td>
<td>扫描的所有表</td>
</tr>
<tr>
<td>*pWhere</td>
<td>where部分语法树</td>
</tr>
<tr>
<td>**ppOrderBy</td>
<td>一个order by语句，或者为空</td>
</tr>
<tr>
<td>wctrlFlags</td>
<td>一个在sqliteInt.h定义的WHERE_ 标志</td>
</tr>
</table>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**4．selectInnerLoop**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数selectInnerLoop的作用该函数主要生成输出结果列的opcode。如果srcTab和nColumn都是零，那么pEList表达式为了获得行数据进行赋值。如果nColumn>0 那么数据从srcTab中拉出，pEList只用于从每一列获得数据类型。selectInnerLoop的主要说明如表3.5所示。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**表3.5  selectInnerLoop的参数及说明**<br>
<table>
<tr>
<td>参数</td>
<td>说明</td>
</tr>
<tr>
<td>*p</td>
<td>完整的select语句被编码</td>
</tr>
<tr>
<td>srcTab</td>
<td>如果不是NULL，使用这个key对结果进行排序</td>
</tr>
<tr>
<td>*pOrderBy</td>
<td>如果不是NULL，使用这个key对结果进行排序</td>
</tr>
<tr>
<td>distinct</td>
<td>如果>=0，确保结果是不同的</td>
</tr>
<tr>
<td>hasDistinct</td>
<td>如果distinct关键字存在返回true</td>
</tr>
<tr>
<td>codeOffset(v, p, iContinue)</td>
<td>添加代码来实现offset</td>
</tr>
<tr>
<td>sqlite3ExprCodeExprList</td>
<td>生成代码，将给定的表达式列表的每个元素的值放到寄存器开始的目标序列。返回元素评估的数量</td>
</tr>
<tr>
<td>sqlite3ReleaseTempReg</td>
<td>释放寄存器，使其可以从用于其他目的。如果一个寄存器当前被用于列缓存，则dallocation被推迟，直到使用的列寄存器变的陈旧</td>
</tr>
<tr>
<td>defined(SQLITE_OMIT_TRIGGER)</td>
<td>丢弃结果。这是用于触发器的select语句。这样选择的目的是要调用用户定义函数。我们不关心实际的选择结果。</td>
</tr>
<tr>
<td>if( pOrderBy==0 && p->iLimit )</td>
<td>如果limit到达，跳转到循环结束。除了，如果有一个分选机，在这种情况下分选机已经限制了我们的输出</td>
</tr>
<table>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**5．sqlite3WhereEnd**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要完成嵌套循环的收尾工作的opcode生成，为每层循环生成OP_Next/OP_Prev，以及关闭表和索引游标的OP_Close。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**6. flattenSubquery**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数flattenSubquery主要实现子查询扁平化。显然对于一个比较复杂的查询，如果满足上面的条件时对这个查询语句进行扁平化处理后就可以实现对查询的优化。函数flattenSubquery具体解释如表3.6如下。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**表3.6  flattenSubquery的参数及说明**
<table>
<tr>
<td>参数</td>
<td>说明</td>
</tr>
<tr>
<td>*pSub</td>
<td>内部查询或“子查询”</td>
</tr>
<tr>
<td>*pSub1</td>
<td>指针移到子查询最右边的选择</td>
</tr>
<tr>
<td>*pSrc</td>
<td>子句的外部查询</td>
</tr>
<tr>
<td>*pSubSrc</td>
<td>子句的子查询</td>
</tr>
<tr>
<td>*pList</td>
<td>外部查询的结果集</td>
</tr>
<tr>
<td>iParent</td>
<td>结果集临时表的VDBE光标数字</td>
</tr>
<tr>
<td>assert( p->pPrior==0 )</td>
<td>无法展平复合查询</td>
</tr>
</table>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**7. SelectExpand**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此函数功能为此例程可以扩展一个SELECT语句及其所有子查询。对于意味着“扩大”一个SELECT语句的附加信息,请参阅上面selectexpand工人回调的评论。扩大一个SELECT语句是处理SELECT语句的第一步。SELECT语句在执行名称解析之前必须扩大。如果出现任何错误,错误消息被写入pparse，调用函数可以通过看pParse->nErr和/或pParse->db->mallocFailed来检测问题。代码如下所示。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;static void sqlite3SelectExpand(Parse *pParse, Select *pSelect){<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  Walker w;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  w.xSelectCallback = selectExpander;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  w.xExprCallback = exprWalkNoop;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  w.pParse = pParse;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  sqlite3WalkSelect(&w, pSelect);<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**8．selectExpander**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;selectExpander函数功能为：把一个Walker回调“扩大”为一个SELECT语句。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(1) 确保vdbe光标号已分配给每个 FROM子句的元素。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; (2) 在定义FROM子句的SrcList中填写 pTabList->a[].pTab字段。当视图出现在From子句时，填写ptablist->[].pselect，用SELECT语句的副本实现了视图。一个副本是由视图的SELECT语句形成的，以便我们可以自由地修改或删除该语句，而不用担心破坏了这个视图的持久性。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; (3) 添加到WHERE子句以适应在链接上的自然关键字和ON以及USING链接子句。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; (4) 扫描结果集(pelist)的列表来查找"*"操作或TABLE.*操作的实例。如果找到,展开每个"*"作为每个表格的每一列，每一个TABLE.*作为TABLE的每一列。

