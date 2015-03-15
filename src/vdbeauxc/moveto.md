# 函数sqlite3VdbeCursorMoveto
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;确保游标P已经准备好读或者写最近的定位到的行。如果遇到OOM错误或者I/O错误是返回错误代码，阻止我们定位光标移动到正确的位置。如果说一个MoveTo指令在给定的光标之前出现，那么我们执行MoveTo指令。如果没有先出现p->deferredMoveto指令那么检查在当前游标下的行是否已经被删除了，如果当前行被删除了标记当前行为NULL，
p->nullRow = 1。如果说游标已经指向正确的行并且也没有被删除，那么这个程序就是一个no-op。
