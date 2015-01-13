# sqlite3Insert()
输入参数：Parse *pParse, SrcList *pTabList, ExprList *pList, Select *pSelect, IdList *pColumn, int onError
返回值：void
函数作用：插入函数所带参数为：解析器环境，我们要插入的表的表名，要插入的列这个函数例程完  成的工作。
解释：具体的工作是：这个例程调用的时候，SQL是遵循以下形式处理的：
(1)insert into TABLE (IDLIST) values(EXPRLIST)/插入值
(2)insert into TABLE (IDLIST) select/复制表，此时的目标表示存在的表名后的IDLIST通常是可选的。如果省略不写，那么表的所有列的列表替代。IDLIST出现在pColumn的参数中。如果IDLIST省略不写，那么pColumn是空值。pList参数处理(1)式中的EXPRLIST,pSelect是可选的。对于（2）式，pList是空的，pSelect是一个用于  插入产生数据的查询语句的指针。
代码的生成遵循以下四个当中的一个模板。对于一个简单的查询语句来说，数据来自一个子句的值，代码的执行一次通过。伪代码如下（我们称之为1号模板）：open write cursor to <table> and its indices/打开游标写到表中和索引中puts VALUES clause expressions onto the stack/把子句值表达式压入栈中write the resulting record into <table> cleanup/把结果记录到表中 清除剩下的3个模板假设语句是下面这种形式的：
//	INSERT INTO <table> SELECT ...
如果查询子句内容是"SELECT * FROM <table2>"这种约束形式，也就是说，如果从一个表中获取所有列的查询,就是没有“where”、“limit”、“group by”和“group by 子句”。如果表1和表2是有相同模式（包括所有相同的索引）的不同的表，那么从表2中复制原始的记  录到表1中涉及到一个特殊的优化。为了实现这//个模板，可以参看这个xferOptimization()函数。这是第二个模板：
//	open a write cursor to <table>
//	open read cursor on <table2>
//	transfer all records in <table2> over to <table>
//	close cursors
//	foreach index on <table>
//	open a write cursor on the <table> index
//	open a read cursor on the corresponding <table2> index
//	transfer all records from the read to the write cursors
//	close cursors
//	end foreach
第三个模板是当第2个模板不能应用和查询子句在任何时候不能读表时。以以下的模板生成代码：
//     EOF <- 0
//       X <- A
//        goto B
//     A: setup for the SELECT
//        loop over the rows in the SELECT
//          load values into registers R..R+n
//         yield X
//        end loop
//        cleanup after the SELECT
//          EOF <- 1
//         yield X
//         goto A
//       B: open write cursor to <table> and its indices
//      C: yield X
//         if EOF goto D
//        insert the select result into <table> from R..R+n
//         goto C
//      D: cleanup
第四个模板，如果插入语句从一个查询语句获取值，但是数据要查入到表中，同时表也是查询语句读  取的一部分。在第三种形式中，我们不得不使用中间表存储这个查询的结果。模板如下：
//  EOF <- 0
//          X <- A
//         goto B
//       A: setup for the SELECT
//          loop over the tables in the SELECT
//           load value into register R..R+n
//            yield X
//          end loop
//         cleanup after the SELECT
//          EOF <- 1
//          yield X
//         halt-error
//       B: open temp table
//       L: yield X
//          if EOF goto M
//          insert row from R..R+n into temp table
//          goto L
//      M: open write cursor to <table> and its indices
//          rewind temp table
//       C: loop over rows of intermediate table
//            transfer values form intermediate table into <table>
//         end loop
//      D: cleanup表值，用作数据源的查询语句，与IDLIST相关的列名，处理连续错误。
