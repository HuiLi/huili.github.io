#  *sqlite3MAppendf()函数
<prre>
 char *sqlite3MAppendf(sqlite3 *db, char *zStr, const char *zFormat, ...){
  va_list ap;
  char *z;
  va_start(ap, zFormat);
  z = sqlite3VMPrintf(db, zFormat, ap);
  va_end(ap);
  sqlite3DbFree(db, zStr);
  return z;
}
功能：这个函数像sqlite3Mprintf()，但调用zStr所指向的 sqlite3DbFree(),并且是经过格式化字符串和返回之前。这个例程的目的是用来修改一个现有的字符串。
例如：x = sqlite3MPrintf(db, x, "prefix %s suffix", x);
函数引用：<img src="p10.png">
</pre>
