# 转换为Julian Day Number所发生的的错误个数
<pre>
根据将一个字符窜转换为Julian Day Number，返回错误个数，输入的字符串类型有1.YYYY-MM-DD HH:MM:SS.FFF  +/-HH:MM  2. DDDD.DD.3. now.第一种格式在数据库中是常见的+/-HH:MM的选项，第二种格式(":SS.FFF")是常见选项，如果字符串是年和日期可以被忽略。
parseDateOrTime（）用来实现该功能，该函数中还调用了setDateTimeToCurrent（）他是用于将VFS即虚拟文件系统所报告的时间转换为当前时间。

</pre>
