# 获取系统当前时间
在最近的windows平台上localtime_s()作为远程连接的一部分，和大部分符合POSIX标准的平台的localtime_r()是相等的
<pre>
。osLocaltime(time_t *t, struct tm *pTm)与函数sqlite3_int64 localtimeOffset（）该函数用来计算时间偏差。该函数中出现了y.Y = sLocal.tm_year + 1900;/*（因为是从1900年开始）*/
  y.M = sLocal.tm_mon + 1;
  y.D = sLocal.tm_mday;
  y.h = sLocal.tm_hour;
  y.m = sLocal.tm_min;
  y.s = sLocal.tm_sec;与linux下的实现是一样的，
</pre>
<img src="date2.png"/>
