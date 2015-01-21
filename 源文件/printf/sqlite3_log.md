# sqlite3_log()函数
<pre>
void sqlite3_log(int iErrCode, const char *zFormat, ...){
  va_list ap;                             变量参数列表
  if( sqlite3GlobalConfig.xLog ){
    va_start(ap, zFormat);
    renderLogMsg(iErrCode, zFormat, ap);
    va_end(ap);
  }
}
功能：这个函数是，如果登录被允许了，就可以格式化并且写消息到这个登录上去。
函数调用：<img src="p16.png">
</pre>
