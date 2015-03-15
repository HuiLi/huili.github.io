# ISNULL
<P>
<img src="pic10.png"/>
<P>
ISNULL使用指定的替换值替换NULL
语法:
      ISNULL(check_expression,replacement_value)
      —参数check_expression
              将被检查是否为NULL的表达式，该参数可以是任意类型的
      —参数replcae_value
              在check-expression为NULL时返回的表达式

     举例：
     ISNULL(price,0.00)
     NOT NULL数据库约束，只能在列级定义，不能在表级定义
     create TABLE emp{
	      eno INT NOT NULL,
	      name VARCHAR(10) NOT NULL,
	      salary NUMBER(6,2)
     } ;
