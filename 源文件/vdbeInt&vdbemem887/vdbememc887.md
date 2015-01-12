# vdbemem.c文件后887行的几个重要函数

## Vdbemem.c的作用

存储需要查询的数据库、表、行列以及某一字段的数据。
完成对这些数据分配内存，编码等步骤。
## Vdbemem.c的几个函数

```
int sqlite3VdbeMemFromBtree()
将btree的数据移入mem结构体
const void *sqlite3ValueText()（数据编码方式）
sqlite3_value *sqlite3ValueNe w()
int sqlite3ValueFromExpr()（表达式返回值）
int sqlite3VdbeMemFromBtree(
  BtCursor *pCur, （记录的游标）
  int offset
  int amt,      （返回的字节数）
  int key,
  Mem *pMem){
  char *zData;
  int available = 0; （可用字节数）
  …}```

将数据从一个b树的键或者值空间中移入一个Mem结构体当中。这个值或者键是从pCur通常指向的入口获取的。pMem结构体被假定没有被初始化，任何之前的内容都将被重写而不是被释放。
```
int sqlite3ValueFromExpr(
  sqlite3 *db,
  Expr *pExpr,（求值表达式）
  u8 enc,     （用于编码）
  u8 affinity，
  sqlite3_value **ppVal（写入新值）
){
  int op;
  …
}```

创建一个新的sqlite3_value对象，这个对象包含pExpr的值。这仅对几个简单的包含以下常量的表达式起作用，如果表示被直接改变为一个值，这个只被分配内存，并写入指针ppval。调用者负责为传递给sqlite3ValueFree()方法的值重新分配内存。如果这个表达式不能被修改，则将指针值为空。
