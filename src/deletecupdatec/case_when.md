# case when
CASE WHEN语句用于计算条件列表并返回多
个可能结果表达式之一

1、简单的CASE函数
语法 CASE input_expression
           WHEN when_expression THEN result_expression
         […n]
    	[
	     ELSE else_expression
	     ]
	     END
input_expression 简单CASE格式所计算的表达式
when_expression 简单CASE格式所比较的简单表达式

2、CASE搜索函数
语法 CASE
           WHEN boolean_expression THEN result_expression
         […n]
    	[
	     ELSE else_result_expression
	     ]
	     END
boolean_expression 使用CASE搜索格式时所计算的布尔表
达式
