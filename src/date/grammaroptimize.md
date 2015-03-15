# 时间戳的语法改良
<pre>
编辑器是以下几种NNN days；NNN hours；NNN minutes；NNN.NNNN seconds；NNN months；NNN years；start of month；start of year；start of week；start of day；weekday N；unixepoch ；localtime ；utc。修改成功返回0，失败返回1，如果出现错误则将错误信息写入context pCtx，如果错误不可识别的错误，则不会写入context pCtx。
如果DateTime *p的内容是以’l’开始，假设”localtime”是否相等，则将系统时间转换成标准时间computeJD()函数与localtimeoffset（）进行相关转换:’u’将当前成员变量iJD作为自从1970年开始的时间，这是unix开始的时间，并且将其转换成为真正julian day number
其他形式一样，但是要注意如果是以”；”结尾这表示（+/-）HH:MM:SS.FFF要减去或者加上特定的小时，分钟，秒，或者微妙。处理时间的参数第一个参数是一个时间戳，第二个参数是一个编辑器。并且交友isDate()函数处理，并且将结果存入结构体DateTime,
实现过程如下：
</pre>
<img src="date3.png"/>
