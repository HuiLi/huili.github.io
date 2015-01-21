#结点单元结构
在SQLite中,结点单元结构是由结构体RtreeCell定义的.

    struct RtreeCell {
      i64 iRowid;
      RtreeCoord aCoord[RTREE_MAX_DIMENSIONS*2];
    }
其中,如果该单元所在结点为叶结点,则iRowid保存对应的rowid.如果该单元所在结点为非叶结点,则iRowid保存了该单元对应的孩子结点号.
aCoord数组保存了该单元对应的维坐标数据。对于每一维数据都会记录一个最大值和最小值,故维坐标数据的数量=最大维度数*2.
#结点的单元数量
结点单元数量等于(结点大小-结点头信息)/每个单元大小
结点最大单元数M=(p->iNodeSize-4)/(p->nBytePerCell)
最小单元数一般为最大单元数的1/3,即m=M/3,此时R树的结点分裂和合并次数最少
