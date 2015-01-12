# 函数sqlite3VdbeResetStepResult
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;对于每一个VDBE都会保持最近的sqlite3_step（执行sql语句，得到返回结果的一行）函数得到的p->rc的值，在qlite3VdbeResetStepResult这个函数的执行会将p->rc的值重置为SQLITE_OK。
