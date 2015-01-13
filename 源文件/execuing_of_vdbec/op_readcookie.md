# OP_ReadCookie
下图是OP_ReadCookie的内容:

![](4-5-5.jpg)

```
case OP_ReadCookie: {
	int iMeta;
	int iDb = pOp->p1;
 	int iCookie = pOp->p3;
sqlite3BtreeGetMeta(db->aDb[iDb].pBt,iCookie,(u32*)&iMeta);
}

```
sqlite3BtreeGetMeta函数是根据p3的值从第iDb号数据库中读取相应的元信息，这里p3为2，所以读取的是数据库格式信息。

