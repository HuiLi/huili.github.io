# renderLogMsg()函数
<pre>
static void renderLogMsg(int iErrCode, const char *zFormat, va_list ap){
  StrAccum acc;                          字符串蓄电池
  char zMsg[SQLITE_PRINT_BUF_SIZE*3];    完整的日志消息

  sqlite3StrAccumInit(&acc, zMsg, sizeof(zMsg), 0);
  acc.useMalloc = 0;
  sqlite3VXPrintf(&acc, 0, zFormat, ap);
  sqlite3GlobalConfig.xLog(sqlite3GlobalConfig.pLogArg, iErrCode,
                           sqlite3StrAccumFinish(&acc));
}
功能：这个例程是格式化sqlite3_log()消息的。我们使用它，是从sqlite3_log()一个单独的例程（）来避免使用对小堆栈系统堆栈空间当禁用日志记录。sqlite3_log()必须呈现为一个静态缓冲区。它不能动态分配内存，因为它可能被称为同时内存分配器,互斥进行。
函数调用： <img src="p15.png">
</pre>
