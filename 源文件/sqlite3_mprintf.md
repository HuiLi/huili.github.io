# *sqlite3_mprintf()函数
<pre>
char *sqlite3_mprintf(const char *zFormat, ...){
  va_list ap;
  char *z;
#ifndef SQLITE_OMIT_AUTOINIT
  if( sqlite3_initialize() ) return 0;
#endif
  va_start(ap, zFormat);
  z = sqlite3_vmprintf(zFormat, ap);
  va_end(ap);
  return z;
}
功能：这个函数式打印进从sqlite3_malloc()中获得的内存中去。省略内部%转换扩展。
函数引用： <img src="p12.png">
</pre>
