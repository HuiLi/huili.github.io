# 主要函数调用关系
where.c中使用SqliteWhereBegin()函数是查询处理部分最核心的函数，它主要完成where部分的优化及相关的opcode的生成。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在SqliteWhereBegin()函数中，它接受从select，update，delete中传入的where子句，然后进行分析、优化，生成操作码，最后把操作码传递给VDBE后，使用SqliteWhereEnd()函数来生成WHERE循环的结束代码。

下面是where.c中的主要函数的调用关系：
<img src="sqliteWhereBegin.png"/>
<pre>
其中：
    sqliteWhereBegin()函数是开始where子句的分析处理；
    WhereClauseInit()函数是初始化whereClause结构体；
    whereSplit()函数是把whereClause根据op分隔开来；
    codeOneLoopStart()函数是为WHERE子句中实现的的第i级循环的代码的生成；
    bestVirtualIndex()函数是用于计算虚拟表的最佳索引；
    bestBtreeIndex()函数是用于选择最佳的Btree索引；
    constructAutomatic()函数是用于创建自动索引；
    exprAnalyzeAll()函数是循环调用exprAnalyze()分析where子句；
    exprAnalyze()函数是分析分隔后的where子句；
    allowedOp()函数是判断相应的运算符是否可以使用索引；
    isLikeOrGlob()函数是判断like或glob语句是否能够进行优化；
    isMatchOfColumn()函数是检查表达式是否是column MATCH expr形式；
    exprAnalyzeOrTerm()函数是分析一个包含两个或更多OR子句连接的子句；
    disableTerm()函数是在WHERE子句中禁用一个被分割的子句；
    findTerm()函数是WHERE子句中查找一个被分割的子句；
</pre>
<img src="sqliteWhereEnd.png"/>
<pre>
其中：
    whereInfoFree()函数是释放whereInfo结构体；
    whereClauseClear()函数是解除一个WhereClause数据结构的分配；
    whereOrInfoDelete()函数是解除WhereOrInfo对象的所有内存分配；
    whereAndInfoDelete()函数是解除WhereAndInfo对象的所有内存分配
</pre>
