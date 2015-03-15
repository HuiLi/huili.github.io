# 写操作
&nbsp;&nbsp;&nbsp;&nbsp;void sqlite3BeginWriteOperation(Parse *pParse,int
setStatement,int iDb)开始进行写操作，期间要进行解析、声明数据库的表的数量。

&nbsp;&nbsp;&nbsp;&nbsp;void sqlite3MultiWrite(Parse *pParse)进行多个写操作，对这些操作设置检查点，从而进行多个数目的写操作。
