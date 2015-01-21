#5.0 整型R树
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认虚表（rtree）用单精度浮点数（4byte）来存放坐标值。如果需要用整型来存放，定义表时需要设置“rtree_i32”

    CREATE VIRTUAL TABLE intrtree
    USING  rtree_i32(id,x0,x1,y0,y1,z0,z1);
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个rtree_i32用32bit有符号整型数存放坐标值。仍然用浮点型数据方法实现rtree算法。对于在没有浮点运算的处理器上跑应用程序，用纯整数实现rtree算法会更加有利。编译时需要设置SQLITE_RTREE_INT_ONLY选项。当SQLITE_RTREE_INT_ONLY被使用时，rtree和rtree_i32的虚表均存放32bit整型数据，并用整型值实现内部运算。
