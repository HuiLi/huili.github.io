# OP_SetCookie
下图是OP_SetCookie的内容:

 ![]( 4-5-10.jpg)
 ```
 case OP_SetCookie : {
	Db *pDb = &db->aDb[pOp->p1];
	pIn3 = &aMem[pOp->p3];
	sqlite3VdbeMemIntegerify(pIn3);
	sqlite3BtreeUpdateMeta(pDb->pBt, pOp->p2, (int)pIn3->u.i);
	pDb->pSchema->schema_cookie = (int)pIn3->u.i;
}
```
OP_SetCookie操作符会根据p1值确定数据库，p1为0说明是对main数据库进行操作，根据p2值确定缓存位，这里p2为5，所以系统建议的页缓存大小为5，经过sqlite3BtreeUpdateMeta函数操作，p3里的内容就写入了main数据库中的页缓存的内存中。pc加1为7，下一个要执行的是aOp[7] OP_CreateTable。
