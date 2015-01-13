# sqlite3GenerateConstraintChecks()
输入参数：  Parse *pParse,      //指向解析器的指针
            Table *pTab,        //要进行插入的表
 			 int baseCur,        //读/写游标的索引在pTab
 			 int regRowid,       //输入寄存器的下标的范围
 			 int *aRegIdx,       //寄存器使用的下标，0是未被使用的指标
int rowidChng,     //true如果rowid和存在记录冲突时
  			int isUpdate,       //true是更新操作，false是插入操作
  			int overrideError,  //重写onError到这，如果不是OE_Default
  			int ignoreDest,     //跳到这个标签在一个OE_Ignore解决
  			int *pbMayReplace   //输出，如果限制可能引起一个代替动作则设置真
返回值：void
函数作用：插入或更新的条件约束检查。
解释：此代码做约束检查当一个插入或更新操作
输入的范围的连贯性表现为以下：
   1.   行ID更新在行更新之后
   2.   数据的第一列在整个记录更新之后更新
   i.   数据来自中间的列的...
   N.  最后一列的数据在整个记录更新之后更新
 那个regRowid参数是寄存器包含的索引，如果参数isUpdate是TRUE并且参数rowidChng不为零则rowidChng包含一个寄存器地址包含rowid 在更新之前发生在isUpdate参数是true表示更新操作，false表示插入操作， 如果isUpdate是false，表明一个insert语句，然而非零的rowidChng参数表明rowid被显示的为insert语句的一部分，如果rowidChng是false意味着rowid是自动计算的在一个插入或是rowid不需要修改的更新操作。这些代码产生由程序，存储新的索引记录到寄存器中通过aRegIdx[]数组来识别。没有记录索引被创建在aRegIdx[i]==0时。指示的顺序在aRegIdx[]与链表（依附于表）的顺序一致，这个程序也会产生代码去检查约束。非空，检查，唯一约束都会被检查。如果约束失效，然后适当的动作被执行。有五种可能的动作：
 ROLLBACK（回滚）, ABORT（终止）, FAIL（失败）, REPLACE（代替）, and IGNORE（忽略）

| 约束类型	| 动作|	说明 |
| -- | -- | -- |
| 任何 	 | 回滚	 | 当前的事务被回滚并且sqlite3_exec()返回立即QLITE_CONSTRAINT的代码 |
| 任何 | 终止 | 	 撤销更改从当前的命令中，只（对于已完成的不进行回滚）然而 由于sqlite3_exec()会立即返回SQLITE_CONSTRAINT代码 |
| 任何	 | 失败 | 	Sqlite3_exec()立即返回一个SQLITE_CONSTRAINT代码，事务不会回滚，并且任何  之前的改变都会保留  |
| 任何 | 	忽略 | 记录的数量和数据出现在堆中，并且立即跳到标签 忽略其他 |
| 非空|	代替   |	空值被默认值代替在那列值中，如果默认值为空这动作与终止相同 |
| 唯一 | 	代替 | 	其他行的冲突是被查入行被删掉 |
| 检查|	代替|	非法的，结果集是一个异常|
哪一个动作被执行取决于被重写的错误的参数。或者如果overrideError==OE_Default，则pParse->onError 参数被使用或者如果pParse->onError==OE_Default则onError的值将被约束使用调用程序必须打开  读/写游标，pTab随着游标的数目计入到baseCur中。
所有的pTab指标必须打开读/写游标随着游标数目baseCur+i对第i个游标，此外，如果不可能有一个代替  动作，则游标不需要打开，在aRegIdx[i]==0。

