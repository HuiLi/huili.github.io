# sqlite3XPrintf()函数
<pre>
void sqlite3XPrintf(StrAccum *p, const char *zFormat, ...){
  va_list ap;
  va_start(ap,zFormat);
  sqlite3VXPrintf(p, 1, zFormat, ap);
  va_end(ap);
}
功能：这个函数是围绕sqlite3VXPrintf()可变参数包装。
</pre>
