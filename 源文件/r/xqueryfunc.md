#6.2 全新xQueryFunc 调用
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;推荐xQueryFunc调用比其它调用从R树查询引擎中接受更多的信息，在它返回值前也给查询引擎发送更多信息。为了保证接口便于管理，xQueryFunc调用用sqlite3_rtree_query_info结构来与查询引擎传递和交换信息。

    struct sqlite3_rtree_query_info {
    void *pContext;                   /* pContext from when function registered */
    int nParam;                       /* Number of function parameters */
    sqlite3_rtree_dbl *aParam;        /* value of function parameters */
    void *pUser;                      /* callback can use this, if desired */
    void (*xDelUser)(void*);          /* function to free pUser */
    sqlite3_rtree_dbl *aCoord;        /* Coordinates of node or entry to check */
    unsigned int *anQueue;            /* Number of pending entries in the queue */
    int nCoord;                       /* Number of coordinates */
    int iLevel;                       /* Level of current node or entry */
    int mxLevel;                      /* The largest iLevel value in the tree */
    sqlite3_int64 iRowid;             /* Rowid for current entry */
    sqlite3_rtree_dbl rParentScore;   /* Score of parent node */
    int eParentWithin;                /* Visibility of parent node */
    int eWithin;                      /* OUT: Visiblity */
    sqlite3_rtree_dbl rScore;         /* OUT: Write the score here */
    };

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3_rtree_query_info结构的前5个成员与sqlite3_rtree_geometry结构相同，并代表同样的含义。该结构包含与XGeom调用相同的nCoord和aCoord成员。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;xQueryFunc必须根据数组aCoord[]中区域是否在指定区域，或与指定区域重叠，或包含在指定区域内的情况来设置sqlite3_rtree_query_info成员eWithin值为NOT_WITHIN, PARTLY_WITHIN或者FULLY_WITHIN，另外，xQueryFunc必须设置成员rScore为非负值来表明查询中那颗子树和记录需要分析和返回。较小的值先被处理。从它的名称可以看出，R树是作为树结构来组织的。每个节点代表一个边界框。根节点是一个包含所有元素的边界框。根节点下是一系列包含记录子集更小边界框的子树。子树之下可能还有子树，以此类推直到叶节点。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个R树查询语句会根据rScore排列优先队列，根节点会排在最前面。查询语句会依次从优先队列中提取最小值。如果提取结果是叶节点（表示它是一个实际记录而非子树）记录将作为结果集的一行返回。如果提取结果是子树节点，那么将它的子树叶节点或者叶节点依次传给xQueryFunc调用验证。xQueryFunc对这些子元素设置eWithin值为PARTLY_WITHIN或者 FULLY_WITHIN，并将其加入到优先队列。一些子元素可能会返回NOT_WITHIN，这些子元素将会丢弃。查询会继续执行直到优先队列为空。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;R树中每个叶节点记录和子树节点都有一个整型等级。叶节点等级一般为0，第一级包含叶节点子树的等级为1，依次类推，根节点等级最大。在sqlite3_rtree_query_info结构中mxlevel成员是R树根节点的等级值。iLevel成员是sqlite3_rtree_query_info给出的查询对象。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;所有R树查询都是使用深度优先遍历。这可以通过设置rScore=iLevel完成。深度优先一般被优先采用，因为它可以使队列元素集最小化，这样可以降低内存需求和提高查询速度。但是，某些应用程序可能会选择广度优先遍历，这时可以设置rScore=mxLevel-iLevel。通过创建更复杂的rScore，应用程序可以更好的控制搜索子树和返回叶节点记录的顺序。比如，在一个有数百万的记录的应用中，rScore应该被设置为利于最大或者最具代表性的数据记录优先返回，以便于应用程序能够迅速的返回最重要的信息，后续返回一些次要的信息。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3_rtree_query_info其它成员在有需要的情况可以为xQueryFunc callback服务。iRowid是查询对象的rowid，iRowid只对叶节点有效。eParentWithin 和rParentScore
是当前包含子树行的eWithin 和rScore的副本。anQueue是一个mxLevel+1的无符号整型数组，用来指明优先队列中每个等级的当前元素序号。

