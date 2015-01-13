# *sqlite3MPrintf()函数
<pre>
 char *sqlite3MPrintf(sqlite3 *db, const char *zFormat, ...){
  va_list ap;
  char *z;
  va_start(ap, zFormat);
  z = sqlite3VMPrintf(db, zFormat, ap);
  va_end(ap);
  return z;
}
功能：这个函数用于打印到从sqliteMalloc()函数获得的内存中去，并且使用内部%转换扩展。
函数引用：<img src="p9.png">
</pre>
