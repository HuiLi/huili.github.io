# 授权检查
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在SQLite授权验证方法  是在编译过程中被调用去验证操作用户对其所访问的数据库中不同位置是否有可读/写的操作权限。在auth.c中包含了一系列检查用户权限的功能函数，其中主要包括sqlite3_set_authorizer()函数和一系列不同类型数据授权检测功能，如:
<br>数据库中表的列信息检测是否可访问
<br>解析树中TK_COLUMN表达式是否可访问
<br>检测给出的编码和参数是否已授权
<br>利用sqlite3AuthContextPush()和sqlite3AuthContextPop函数保存授权信息(authorization context)
