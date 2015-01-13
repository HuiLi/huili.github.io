# 正常时间与儒略日数之间的相互转换
该功能首先由parseHhMmSs（）函数将其转换成为HH:MM:或者HH:MM:SS表示可以用三位进行表示具体差异只是他的精确度不一样。
<pre>
if( getDigits(zDate, 2, 0, 24, ':', &h, 2, 0, 59, 0, &m)!=2 ){/*The get_Digits method gets the gathered digits for the call.*/
    return 1;
 }/*读取了HH:MM*/
if( getDigits(zDate, 2, 0, 59, 0, &s)!=1 ){
      return 1;/*读取SS */
p->s = s + ms;/*将所读入的数据存入DateTime *p*/
</pre>

<pre>
最后还要看一看是否有因为时区原因有偏移量的p->validTZ = (p->tz!=0)?1:0;/*偏移量*/从正常时间向儒略日转换computeJD(DateTime *p)完成注意如果该函数为特别注年月日则从2000-01-01开始YYYY-MM-DD HH:MM:SS.FFF; YYYY-MM-DD HH:MM:SS;YYYY-MM-DD HH:MM
;YYYY-MM-DD函数成功将结果存入DateTime结构体，成功返回0，否则返回1.
 而将儒略日向正常时间转换这用到了函数computeYMD(DateTime *p)。该函数的调用过程见上图所示。首先检验看是否有偏移量，如果没有就将其设置为2000.1.1。其后就是按照数学公式进行计算。该函数只是完成了向年月日的转换，为完成向时分秒的转换就要调用computeHMS(DateTime *p)函数。
 </pre>
 调用过程如下图:
 <img src="date1.png"/>

