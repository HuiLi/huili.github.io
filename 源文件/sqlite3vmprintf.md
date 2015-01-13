# *sqlite3VMPrintf()函数
<pre>
char *sqlite3VMPrintf(sqlite3 *db, const char *zFormat, va_list ap){
  char *z;
  char zBase[SQLITE_PRINT_BUF_SIZE];
  StrAccum acc;
  assert( db!=0 );
  sqlite3StrAccumInit(&acc, zBase, sizeof(zBase),db->aLimit[SQLITE_LIMIT_LENGTH]);
  acc.db = db;
  sqlite3VXPrintf(&acc, 1, zFormat, ap);
  z = sqlite3StrAccumFinish(&acc);
  if( acc.mallocFailed ){
    db->mallocFailed = 1;
  }
  return z;
}
功能：这个函数用于打印到从sqliteMalloc()函数获得的内存中去，并且使用内部%转换扩展。
函数引用：<img src="p8.png">
</pre>
