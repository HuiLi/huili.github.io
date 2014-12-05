#6.0 定制R树查询
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在标准SQL语句中selecct子句的where条件，一个程序可以查询在R树中具有空间关系的记录（它们互相相交或者包含在一个特殊的边界区域中）。自定义的R树查询，在select语句的where子句中使用匹配操作符查询允许应用程序查询R树中任意区域或形状。这个功能非常有用，比如，可以容易计算R树中3D空间中相机摆放位置对象的子集。
应用程序由RTree geometry callbacks执行R树定制区域查询，两个API 实现和注册查询：

     int sqlite3_rtree_query_callback(
     sqlite3 *db,
     const char *zQueryFunc,
     int (*xQueryFunc)(sqlite3_rtree_query_info*),
     void *pContext,
     void (*xDestructor)(void*)
    );
     int sqlite3_rtree_geometry_callback(
     sqlite3 *db,
     const char *zGeom,
     int (*xGeom)(sqlite3_rtree_geometry *, int nCoord, double *aCoord, int *pRes),
     void *pContext
    );
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3_rtree_query_callback()适用于3.8.5版本，它是首选的接口。sqlite3_rtree_geometry_callback()是过时的，灵活性较差的接口，它向后兼容。
在两个API上的调用会创建一个由第2个参数(zQueryFunc or zGeom)命名的新SQLite函数，当该函数出现在任何R树虚表列匹配操作符的右侧或左侧时，如果一个特殊对象或子树与指定区域重叠，调用由第3个参数(xQueryFunc or xGeom)定义。
比如，下面的查询找到与一个圆点为45.3,22.9，半径为5.0的圆重叠的所有记录：

    SELECT id FROM demo_index
    WHERE id MATCH circle(45.3, 22.9, 5.0)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;定制R树查询语句的SQL符号与接口无关，sqlite3_rtree_geometry_callback() 或 sqlite3_rtree_query_callback()都是用来注册SQL函数。但是新类型的函数调用可以让应用程序更灵活的控制查询结果。

