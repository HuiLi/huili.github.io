# *sqlite3_vsnprintf()函数
<pre>
char *sqlite3_vsnprintf(int n, char *zBuf, const char *zFormat, va_list ap){
  StrAccum acc;
  if( n<=0 ) return zBuf;
  sqlite3StrAccumInit(&acc, zBuf, n, 0);
  acc.useMalloc = 0;
  sqlite3VXPrintf(&acc, 0, zFormat, ap);
  return sqlite3StrAccumFinish(&acc);
}
char *sqlite3_snprintf(int n, char *zBuf, const char *zFormat, ...){
  char *z;
  va_list ap;
  va_start(ap,zFormat);
  z = sqlite3_vsnprintf(n, zBuf, zFormat, ap);
  va_end(ap);
  return z;
}
功能：sqlite3_snprintf（）的作用像函数snprintf（），不同之处在于它忽略了当前区域设置。这是SQLite的重要，因为我们无法在通过一些特定的语言环境中使用“，”作为在这里的小数点。
注意：sqlite3_snprintf（）的前两个参数是参照snprintf（）的标准。不幸的是，在不破坏兼容性时，它是不会改变什么的，所以我们这种错误时无关紧要的。
sqlite3_vsnprintf（）是可变参数版本。
函数引用： <img src="p13.png">
           <img src="p14.png">

</pre>
