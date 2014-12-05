#3.0 使用Rtree模块
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite Rtree模块是用虚表实现的。每个Rtree索引都是一个3-11奇数列的虚表。第一列一般为64bit有符号整型主键。其他列各自组成一对，每对代表一维，包含该维的最大或最小值。因此，一维索引有三列，二维有5列，三维有7列…..五维Rtree有11列。SQLite最多支持5维Rtree索引。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
SQLite Rtree第一列应该为一个整型主键。rtree虚表中最大/最小值对存放32bitfloat数，而对于rtree_i32虚表则存放32bit有符号整型数。区别于其他SQLite表可以存放各种类型的数据，R*tree虚表执行严格的存储类型定义。如果尝试向第一列插入一个非整型数，或者向其他列插入一个非数值型数，都会返回一个错误。
