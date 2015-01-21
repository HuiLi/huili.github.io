#结点结构
在SQLite中R树结点结构是由结构体RtreeNode定义的.

    struct RtreeNode {
      RtreeNode *pParent;          //父结点指针
      i64 iNode;                   //结点子树高度
      int nRef;
      int isDirty;
      u8 *zData;
      RtreeNode *pNext;            //hash链的下一结点
    }
其中,nRef是该结点的引用次数,用来衡量是否需要将该结点从hash链中删除,isDirty是用来确认该结点是否已被修改.将该结点从hash链删除前如果isDirty=1,则需要将结点数据写入到数据库中.
zData保存了结点相关信息.如果该结点为根结点,则zData前2byte保存了树的高度信息,如果该结点非根节点,则前2byte未使用.zData[2-3]保存了该结点的单元数.
