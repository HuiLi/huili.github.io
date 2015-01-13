# 事务的开始
事务以下面的函数进行开始操作:
void sqlite3BeginTransaction(Parse *pParse, int type){
sqlite3 *db;
Vdbe *v;
int i;
}
 工作流程如下：

（1）如果 db->aDb[0].pBt等0 则返回

（2）做一个授权检查使用给定的代码和参数。返回SQLITE_OK(零)或SQLITE_IGNORE SQLITE_DENY。如果SQLITE_DENY返回,那么错误数和错误消息pParse适当修改不做任何授权检查如果initialising数据库或者从sqlite3_declare_vtab内部解析器调用。

（3）得到一个VDBE给定解析器的上下文。在必要时创建一个新的。如果出现错误,返回NULL,pParse留言。

（4）添加一个新的指令VDBE指示当前的列表。返回新指令的地址。

（5）参数:p 为VDBE指针Op是这个指令的操作码p1,p2,p3是操作数。

（6）使用sqlite3VdbeResolveLabel()函数来解决一个地址和sqlite3VdbeChangeP4()函数来改变P4操作数的值。

（7）声明的Vdbe BTree对象。事先准备好的语句需要知道完整的组连接数据库,将被使用。这些数据库的面具是在p->btreeMask维护。p->ockMask值p->btreeMask数据库的子集,将需要一个锁。
