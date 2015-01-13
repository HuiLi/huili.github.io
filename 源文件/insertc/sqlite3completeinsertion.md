# sqlite3CompleteInsertion()
输入参数：  Parse *pParse,      //指向解析器的指针
 			 Table *pTab,       //我们要插入的表
  			 int baseCur,        //读/写指针指向pTab指数
  			 int regRowid,       //内容的范围
 			 int *aRegIdx,       //寄存器使用的每一个索引，0表示未被使用索引
 			 int isUpdate,       //TRUE是更新，FALSE是插入
 			 int appendBias,     //如果这可能是一个附加，则值为TRUE
 			 int useSeekResult //在OP_[Idx]插入，TRUE设置USESEEKRESULT标记
返回值：void
函数作用：程序产生代码来完成插入或者更新操作，之前的函数是生成指令集，此函数是可以正确插入之后要做的。开始通过之前称为sqlite3GenerateConstraintChecks
寄存器连续的范围开始在regRowid	包含rowid和要被插入的数据程序的参数应该和sqlite3GenerateConstraintChecks的前六个参数一样。
解释：此函数在sqlite3Insert被调用，主要是用来断定插入操作的完成。
