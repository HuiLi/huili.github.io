# 实现的技术细节
    static int btreeCreateTable(Btree *p, int *piTable, int createTabFlags)
1. 创建一个btree表格
2. 移动现有数据库为新表的根页面腾出空间
3. 用新根页更新映射寄存器和metadata
4. 新表根页的页号放入PgnoRoot并写入piTable

流程图如下：
 <img src="btreeCreateTable.jpg">


    int sqlite3BtreeInsert(  BtCursor *pCur, const void *pKey, i64 nKey,  const void *pData, int nData, int nZero,  int appendBias, int seekResult)
向Btree中插入一个新记录：

1. 关键字按照(pKey,nKey)给定，数据按照(pData,nData)给定。
2. 找到结点插入位置
3. 分配内存空间
4. 插入结点

流程图如下：

 <img src="Insert.jpg">


    static int btreeDropTable(Btree *p, Pgno iTable, int *piMoved)
1. 删除表中所有信息
2. 将表的根页放入自由列表
3. 游标不能为打开状态
4. 更新最大的根页

流程图如下：
 <img src="droptable.jpg">


