# 释放资源
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在程序执行完毕后需要清理虚拟机，从而释放资源。该部分由三个函数来完成：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;void sqlite3VdbeFreeCursor(Vdbe *p, VdbeCursor *pCx){}
该函数用来关闭VDBE游标并且释放该游标占用的所有资源。
static void closeAllCursors(Vdbe *p){}
该函数用来关闭所有游标，并且释放vdbe中任何被虚拟机占用的动态内存。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;static void Cleanup(Vdbe *p){}

该程序能够自动关闭任何被打开的游标，列表和分类器。同时，该程序能够删除aVar[]数组里变量的值。
