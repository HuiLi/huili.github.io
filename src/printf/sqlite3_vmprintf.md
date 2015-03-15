# *sqlite3_vmprintf()函数
<pre>
char *sqlite3_vmprintf(const char *zFormat, va_list ap){
  char *z;
  char zBase[SQLITE_PRINT_BUF_SIZE];
  StrAccum acc;
#ifndef SQLITE_OMIT_AUTOINIT
  if( sqlite3_initialize() ) return 0;
#endif
  sqlite3StrAccumInit(&acc, zBase, sizeof(zBase), SQLITE_MAX_LENGTH);
  acc.useMalloc = 2;
  sqlite3VXPrintf(&acc, 0, zFormat, ap);
  z = sqlite3StrAccumFinish(&acc);
  return z;
}
功能：这个函数式打印进从sqlite3_malloc()中获得的内存中去。省略内部%转换扩展。
函数引用： <img src="p11.png">
</pre>
