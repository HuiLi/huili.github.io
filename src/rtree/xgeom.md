#6.1 旧版本xGeom回调函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Legacy XGeom回调需要4个参数，第一个参数是一个指向sqlite3_rtree_geometry结构的指针，它提供了SQL函数如何调用的信息。第二个参数是R树中每个记录的坐标数，对于给定的R树一般都是相同的。1维R树的坐标数为2，2维R树的坐标数为4，3维R树的坐标数为6，以此类推。第3个参数是当前需要查询区域的坐标数组aCoord[]。最后一个参数是需要写入回调结果的指针。如果数组aCoord[]定义的区域不在xGeom callback指定的区域内，结果返回zero，如果数组aCoord[]定义的区域与xGeom callback指定区域重叠或者在指定区域之内，则返回non-zero。一般xGeom callback返回SQLITE\_OK，如果返回值不是SQLITE_OK，表示R树查询因错误而终止。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;xGeom回调的第一个参数Sqlite3\_rtree\_geometry是如下的结构。在相同的语句相同的匹配查询中相同的调用的结构是一样的。sqlite3_rtree_geometry结构内容由SQLite初始化，随后不会更改。如果需要，调用过程会在pUser和xDelUser之间自由切换。

    typedef struct sqlite3_rtree_geometry
    sqlite3_rtree_geometry;
    struct sqlite3_rtree_geometry {
    void *pContext; /* Copy of pContext passed to s_r_g_c() */
    int nParam;     /* Size of array aParam */
    double *aParam; /* Parameters passed to SQL geom function */
    void *pUser;  /* Callback implementation user data */
    void (*xDelUser)(void *); /* Called by SQLite to clean up pUser */
    }

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当回调函数被注册时，sqlite3_rtree_geometry结构成员pContext一般直接设置为sqlite3_rtree_geometry_callback()中参数pContext的副本。数组aParam[]和参数(size nParam)包含了SQL函数中匹配操作符右侧的参数值。在例子circle查询语句中，nParam设置为3，数组aParam[]包含3个值45.3, 22.9 和 5.0。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3_rtree_geometry结构成员pUser和xDelUser初始时被设置为NULL。pUser可能被设为任意有利于后续类似查询调用的值（比如，一个用于处理区域交叉查询的复杂结构的指针）。如果xDelUser被设置为non-NULL，在查询结束后SQLite自动将pUser值作为参数传递给xDelUser。换言之，xDelUser作为pUser的析构函数。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;xGeom callback在R树中一般都是深度优先搜索。
