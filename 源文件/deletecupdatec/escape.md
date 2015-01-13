# ESCAPE
<P>
<img src="pic5.JPG"/>
<p>
ESCAPE用于转译：
select * from test where name like ‘_%%’;
select * from test where name like ‘_\%%’;
select * from test where name like ‘_*%%’ ESCAPE ‘*’;
 (自定义转译符*)

REGEXP匹配被测试值的开头与结尾，在模式开始处使
用’^’，在模式的结尾用’$’

<P>
<img src="pic6.JPG"/>
<P>
