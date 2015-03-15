# 创建vdbe结构体
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该功能由函数Vdbe *sqlite3VdbeCreate(sqlite3 *db)实现
其中参数db是需要创建vdbe数据库的句柄，从上面的函数可以看出它所描述的是：每个数据库连接中可能有多个活动的虚拟机，这些虚拟机被组织成一个双向链表，pPrev和pNext域分别指向链表中的前趋和后继。在sqlite3结构中有一个域pVdbe，为指向此双向链表的头指针。新创建的虚拟机插在该双向链表的头部。
