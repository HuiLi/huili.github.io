# 简析打开数据库流程
<img src="2.png">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如该流程图所示，SQLite数据库文件打开过程中需要使用到shell.c，main.c，btree.c，pager.c，os.c和os_win.c模块中的相关函数，最后是由os_win.c模块中的winOpen()函数予以在widows平台上实现对数据库打开，读写等大多数功能。
