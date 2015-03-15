# 设置或清除访问授权
函数说明
<pre>int sqlite3_set_authorizer(sqlite3 *db,
  int (*xAuth)(void*,int,const char*,const char*,const char*,const char*), void *pArg)</pre>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此源码主要实现了sqlite3_set_authorizer()函数用来注册第一个参数中指定的数据库的授权者回调信息，在编译过程中会逻辑创建不同的点去执行不同的操作，而授权者回调函数被调用去验证这些操作是否被允许，并且返回状态信息SQLITE_OK,SQLITE_IGNORE,SQLITE_DENY，SQLITE_OK表示操作被允许，SQLITE_IGNORE指该特殊操作部被允许，但它所属的SQL语句将继续编译,SQLITE_DENY则表示整个SQL语句将由于错误被拒绝。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（函数中利用互斥体机制来保证过程不受其他数据库影响，通过Sqlite3数据  库结构体执行 db->xAuth = xAuth;db->pAuthArg = pArg;进行授权。）

工作流程<br>
（1）输入一个互斥体连接db->mutex，通过数据库结构体执行访问授权函数 db->xAuth和设置授权函数的第一个参数db->pAuthArg<br>
（2）检查db中已准备好的语句标记是否被过期，过期的声明需  要被重编译<br>
（3）退出之前连接的互斥体db->mutex，返回SQLITE_OK
