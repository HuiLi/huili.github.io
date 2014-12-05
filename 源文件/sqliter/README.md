# SQLiteR树结构
在SQLite中R树结构是由结构体Rtree定义的.

    struct Rtree {
      sqlite3_vtab base;//虚表基本结构
      sqlite3 *db;                //数据库连接
      int iNodeSize;              //结点大小
      int nDim;                   //维数
      int nBytesPerCell;          //结点每个单元大小
      int iDepth;                 //树的高度
      char *zDb;                  //R树表的数据库信息
      char *zName;                //表名
      RtreeNode *aHash[HASHSIZE]; //内存结点hash链
      int nBusy;                  //当前用户数
      RtreeNode *pDeleted;        //结点删除标记
      int iReinsertHeight;        //重插入高度
      ............
      int eCoordType;             //坐标数据类型
    }
