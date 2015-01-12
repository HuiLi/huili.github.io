# date.c模块源码分析（1）-（5）
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite 支持如下下面 5 个日期与时间函数：
<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;date(timestring, modifier, modifier, ...)<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;time(timestring, modifier, modifier, ...)<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;datetime(timestring, modifier, modifier, ...)<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;julianday(timestring, modifier, modifier, ...)<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;strftime(format, timestring, modifier, modifier, ...)<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这 5 个函数都接受一个时间字符串作为参数， 紧跟着的是可选的修饰符。 strftime 函数还接受一个格式化字符串作为它的第一个参数。<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这些时间日期函数使用ISO-8601规定的时间日期格式的子集，date函数返回 YYYY-MM-DD格式的日期，time函数返回HH:MM:SS格式的时间， 而datetime则返回"YYYY-MM-DD HH:MM:SS" 格式的日期时间； julianday函数则返回自儒略日（公元前4713年1月1日）的天数；strftime返回的格式则由其参数决定。<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;date.c是sqlite的代码生成器部分（Code Generator），占24031byte大小,该模块实现的是与日期和时间转换有关的函数，并且没有运用太多的理论体系，运用最多的是时间转换的数学公式。<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite处理所有时间和日期作为儒略日数。这日期和时间被保存从格林威治时间公元前4714年11月24号中午起，根据公历体系。公历系统是用于所有的日期和时间，即使那些早于公历。历史学家通常使用儒略日日历表示在1582-10-15之前 和之后的事情，注意这一差异。其中年被4个数字表示，意味着日期只能被表示在0000-01-01和9999-12-31之间，即使儒略日数允许表示更大范围内的日期。 例如：1970-01-01 00:00:00用儒略日表示是2440587.5 ，2000-01-01 00:00:00用儒略日表示是2451544.5。

    typedef struct DateTime DateTime; /*struct DateTime结构体重命名为DateTime*/
    struct DateTime {
    sqlite3_int64 iJD; /* The julian day number times 86400000 儒略日表示一天的毫秒数*/
    int Y, M, D;       /* Year, month, and day 定义变量年，月，日*/
    int h, m;          /* Hour and minutes 定义变量小时和分钟*/
    int tz;            /* Timezone offset in minutes 在分钟的时区偏移*/
    double s;          /* Seconds 定义变量秒*/
    char validYMD;     /* True (1) if Y,M,D are valid 判断年月日是否有效*/
    char validHMS;     /* True (1) if h,m,s are valid 判断小时、分钟和秒是否有效*/
    char validJD;      /* True (1) if iJD is valid 判断毫秒数iJD是否有效*/
    char validTZ;      /* True (1) if tz is valid 判断偏移量tz是否有效*/
    };

（1）获取系统读数（getDigits）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数通过va_arg对可变参数的使用，读取系统时间并将其结果存储到*zDate指定的存储空间，并用于下面的循环中，最后返回函数成功循环的次数，其中用到了va_list定义一个变量ap，变量ap是指向参数的指针；然后用va_start宏初始化变量刚定义的va_list变量ap；再用va_arg返回可变的参数，va_arg的第二个参数是你要返回的参数的类型；最后用va_end宏结束可变参数的获取。

      va_list ap;      /*定义一具va_list型的变量ap，这个变量ap是指向参数的指针*/
      int cnt = 0;                        /*记录函数循环的次数*/
      va_start(ap, zDate);    /*用va_start宏初始化变量刚定义的va_list变量ap*/
      do{
    N = va_arg(ap, int);
    /*用va_arg返回可变的参数，va_arg的第二个参数是返回的参数的类型*/
        min = va_arg(ap, int);
        max = va_arg(ap, int);
        nextC = va_arg(ap, int);
        pVal = va_arg(ap, int*);
        val = 0;
      va_end(ap);      /*用va_end宏结束可变参数的获取*/

（2）获取时区差异扩展的表示形式（parseTimezone）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 扩展的形式如下： (+/-)HH:MM或 “zulu”的符号形式如下：Z
     如果函数最后分析成功，把改变后的分钟数写到偏移量P - > TZ中并返回0；如果分析发生一个错误时，返回非零。

        if( getDigits(zDate, 2, 0, 14, ':', &nHr, 2, 0, 59, 0, &nMn)!=2 ){
     /*对getDigits函数的调用并判断*/
      return 1;
      }
      zDate += 5;     /*HH:MM读取了五位*/
      p->tz = sgn*(nMn + nHr*60);   /*把改变后的分钟数写到偏移量P - > TZ中*/

（3）解析时间形式（parseHhMmSs）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该函数将时间解析为HH:MM或HH：MM：SS或HH：MM：ss.ffff的形式。如果有语法错误就返回1，正确的就返回0。

（4）把YYYY-MM-DD HH:MM:SS转换为儒略日（computeJD）
我们总是假定YYYY-MM-DD是根据公历的。根据儒略日计算时间公式，把正常时
   间（YYYY-MM-DD HH:MM:SS）转换为儒略日时间。其中， 如果年月日没有指定，
   我们就假定时间从2000年1月1日开始的。

   （5）获取系统当前所需时间形式
   <img src="time.png">
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;计算当地时间与UTC（即格林尼治标准时间）时间值间的差异（在毫秒上）是通过函数localtimeOffset实现的；isDate函数是处理时间函数的参数。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;parseModifier函数是实现对日期时间戳的改良方法，编辑器是以下几种NNN days；NNN hours；NNN minutes；NNN.NNNN seconds；NNN months；NNN years；start of month；start of year；start of week；start of day；weekday N；unixepoch ；localtime ；utc。前 6 个修饰符用于向时间字符串或修饰符指定的日期时间添加指定数量的时间。 注意 "±NNN months" 的工作方式是： 先将原来的日期渲染成 YYYY-MM-DD 格式， 对月份添加 ±NNN 的值， 然后再对结果进行修正。 如果 2001-03-31 添加了 '+1 month' 修饰符， 则应先得到 2001-04-31 ， 由于 4 月只有 30 天， 所以结果会被修正为 2001-05-01 。 如果向闰年的 2 月 29 日添加了 '±N years' 修饰符， 当 N 不是 4 的倍数时， 也会发生这样的修正。第 7 到 9 的那些 "start of" 修饰符将日期转换为当前日、 月、 年的开始时间。修饰符 "weekday" 将日期向后推进直到下一周的指定天数。 星期天是 0 ， 星期一是 1 ， 依此类推。第 11 个修饰符 "unixepoch" 只有在日期格式为 "DDDDDDDDDD" 时才有效， 将 unix 时间戳转换成时间， 由于参数是 64 位整数， 因此， 有效地时间范围是 0000-01-01 00:00:00 至 5352-11-01 10:52:47 (Unix 时间 -62167219200 至 10675199167)修饰符 "localtime" 将 utc 时间转换为本地时间， "utc" 则相反。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;下面的函数实现SQLite对不同的日期和时间处理的功能： juliandayFunc（返回julianday时间格式）、datetimeFunc（YYYY-MM-DD HH:MM:SS时间格式）、timeFunc（返回HH:MM:SS时间格式）、dateFunc（返回YYYY-MM-DD时间格式）和strftimeFunc函数（返回以字符串形式的时间格式）通过对isDate函数的调用，返回系统所需要的当前时间的形式。







