# 读取时区差异扩展表示格式
用parseTimeZone()实现该功能，最后所确定的格式为（+/-）HH:MM，或者一个zulu标记Z,如果编译成功则tz或者0，如果编译错误，则返回一个非0值。具体如下：
<pre>
while( sqlite3Isspace(*zDate) ){ zDate++; }
  p->tz = 0;
  c = *zDate;
  if( c=='-' ){
sgn = -1;
  }else if( c=='+' ){
    sgn = +1;
  }else if( c=='Z' || c=='z' ){
    zDate++;
    goto zulu_time;
  }else{
    return c!=0;
  }/*(+/-)或者 Z是时间要开始表示的标志*/
zDate++;/*向前移动一位正式开始*/
zDate += 5;/*HH:MM读取了五位*/
</pre>
