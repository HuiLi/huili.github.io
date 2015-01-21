# 分析delete.c和update.c

该部分主要分析SQLITE中delete.c和update.c
delete语句用于删除表中的行

<p>
<img src="pic1.png"/>
<p>

update用于修改表中的数据

<p>
<img src="pic2.png"/>
<p>

<p>
<img src="pic3.png"/>
<p>

literal-value 表达式的类型
bind-parameter 绑定参数类型
unary-operator与binary-operator为一元操作符和二元操作符
DISTINCT关键字消除所有重复的记录，只保留一条记录