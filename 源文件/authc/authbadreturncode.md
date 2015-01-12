# 授权返回一个非法的值
2.1函数头

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;static void sqliteAuthBadReturnCode(Parse *pParse)

2.2工作流程

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（1）写一个错  误的信息为pParse->zErrMsg说明用户提供授权返回一个非法的值.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（2）返回SQLITE_ERROR
