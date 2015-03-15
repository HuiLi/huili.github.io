# 列出虚拟机中的程序清单
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该功能由函数int sqlite3VdbeList(  Vdbe *p ){}来实现。
该函数用来在虚拟机中给出程序运行的清单，也就是说罗列出在程序运行的过程中使用了哪些方法。需要注意的是，该函数并不是运行代码，而是将每条指令回调一次。这个功能用来实现解释——explain。
explain指令用来解释sql语句的执行过程。Explain有两种状态，p->explai==1和p->explain==2。当p->explain==1时，每条指令都被列举出来。当p->explain==2时，只有OP_Explain指令被列举出来并且使用不同的格式展示。p->explain==2被用来解释查询计划。当p->explain==1时，首先，主程序被列举出来，然后每一个触发子程序被依次列举出来。
	当输出的行数达到最大值nRow时，意味着列举工作已经完成并且sqlite3_step()应该返回SQLITE_DONE。nRow是主程序中的行数的总和，将这个总和加到所有遇到的触发子程序上。当出现新的触发子程序时  nRow值将增加，但是p->pc最终会赶上nRow。
