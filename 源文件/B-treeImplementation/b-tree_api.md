# B-tree API
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;B-tree模块有自己的API，它们是独立于C API的。这些API主要是针对SQLite需求量身定做的，因此对于其他的数据库并不适用。要想更加了解SQLite内部机制，先了解这些API是很有必要的。B-tree的另一个优点是本身事务机制、pager处理事务、锁以及日志文件，并为B-tree模块提供支持。B-tree API根据目的大体划分成以下函数：存取及事务函数（如图6所示 ），表函数 （如图7所示 ），游标函数（如图8所示 ） ，记录函数（如图9所示 ），配置管理函数 （如图10所示 ）。
<img src="存取及事务函数.jpg">
<img src="表函数.jpg">
<img src="游标函数.jpg">
<img src="记录函数.jpg">
<img src="配置管理函数.jpg">
