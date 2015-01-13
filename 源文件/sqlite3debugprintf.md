# sqlite3DebugPrintf()函数
<pre>
void sqlite3DebugPrintf(const char *zFormat, ...){
  va_list ap;
  StrAccum acc;
  char zBuf[500];
  sqlite3StrAccumInit(&acc, zBuf, sizeof(zBuf), 0);
  acc.useMalloc = 0;
  va_start(ap,zFormat);
  sqlite3VXPrintf(&acc, 0, zFormat, ap);
  va_end(ap);
  sqlite3StrAccumFinish(&acc);
  fprintf(stdout,"%s", zBuf);
  fflush(stdout);
}
功能：这个函数式一个版本的printf()，是用来理解％lld的，并用于调试。这个版本中， printf()内置于某些版本的Windows中，如果你给它一个很长很长整型，它是不明白％lld和段错误的。
函数调用： <img src="p17.png">
</pre>
