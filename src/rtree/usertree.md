#3.0 使用Rtree模块
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite Rtree模块是用虚表实现的。每个Rtree索引都是一个3-11奇数列的虚表。第一列一般为64bit有符号整型主键。其他列各自组成一对，每对代表一维，包含该维的最大或最小值。因此，一维索引有三列，二维有5列，三维有7列…..五维Rtree有11列。SQLite最多支持5维Rtree索引。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
SQLite Rtree第一列应该为一个整型主键。rtree虚表中最大/最小值对存放32bitfloat数，而对于rtree_i32虚表则存放32bit有符号整型数。区别于其他SQLite表可以存放各种类型的数据，R\*tree虚表执行严格的存储类型定义。如果尝试向第一列插入一个非整型数，或者向其他列插入一个非数值型数，都会返回一个错误。<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Rtree 模块包含在源码中，但默认是不编译的。为了启用R\*tree模块，编译时设置SQLITE_ENABLE_RTREE即可。对于大多数编译器，只需在命令行增加“-DSQLITE_ENABLE_RTREE=1”
<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认虚表（rtree）用单精度浮点数（4byte）来存放坐标值。如果需要用整型来存放，定义表时需要设置“rtree_i32”

    CREATE VIRTUAL TABLE intrtree
    USING  rtree_i32(id,x0,x1,y0,y1,z0,z1);
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个rtree\_i32用32bit有符号整型数存放坐标值。仍然用浮点型数据方法实现rtree算法。对于在没有浮点运算的处理器上跑应用程序，用纯整数实现rtree算法会更加有利。编译时需要设置SQLITE_RTREE_INT_ONLY选项。当SQLITE_RTREE_INT_ONLY被使用时，rtree和rtree_i32的虚表均存放32bit整型数据，并用整型值实现内部运算。
