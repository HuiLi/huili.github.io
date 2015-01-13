# 主要结构体
结构体struct tm和结构体DateTime，他的具体定义如下
<pre>
struct tm{
    int tm_sec;  秒–取值区间为[0,59]
    int tm_min;  分 - 取值区间为[0,59]
    int tm_hour;  时 - 取值区间为[0,23]
    int tm_mday;  一个月中的日期 - 取值区间为[1,31]
    int tm_mon; 月份（从一月开始，0代表一月） - 取值区间为[0,11]
    int tm_year;  年份，其值从1900开始
    int tm_wday; 星期–取值区间为[0,6]，其中0代表星期天，1代表星期一，以此类推
    int tm_yday; 从每年的1月1日开始的天数–取值区间为[0,365]，其中0代表1月1日，1代表1月2日，以此类推
    int tm_isdst; 夏令时标识符，实行夏令时的时候，tm_isdst为正。不实行夏令时的进候，tm_isdst为0；不了解情况时，tm_isdst()为负。
    long int tm_gmtoff; 指定了日期变更线东面时区中UTC东部时区正秒数或UTC西部时区的负秒数
    const char *tm_zone; 当前时区的名字(与环境变量TZ有关)
};
struct DateTime {
    sqlite3_int64 iJD; /* The julian day number times 86400000 *//*一天的毫秒数*/
    int Y, M, D;       /* 年月日*/
    int h, m;          /* 小时，分钟*/
    int tz;            /* Timezone offset in minutes */
    double s;          /* 秒数 */
    char validYMD;     /* 如果 Y,M,D 有效则位真*/
    char validHMS;     /* 如果 h,m,s 有效则位真*/
    char validJD;      /* 如果iJD 有效为真 */
    char validTZ;      /* 如果tz有效则为真 */
};
</pre>
