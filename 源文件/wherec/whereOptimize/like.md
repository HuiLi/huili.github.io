# LIKE语句优化
LIKE或GLOB语句进行优化时，需要满足一下条件：
1. LIKE或GLOB运算符的左边必须是有索引的列名且类型必须是TEXT类型的亲和类型；
2. LIKE或GLOB运算符的右边必须是一个字符串文字或一个绑定了字符串文字的参数，并且不能由通配符开头；
3. ESCAPE子句不能出现在LIKE运算符中；
4. 对于GLOB运算符，列必须使用内置BINARY核对序列排序；
5. 对于LIKE运算符，如果case_sensitive_like模式是启用的（也就是区分大小写的），那么列必须使用BINARY核对序列排列。
如果满足了上述条件，我们则可以添加一些不等式约束来减少LIKE或GLOB的查询范围。
例如：
x LIKE 'abc%'
添加条件后变为：
x >= 'abc' AND x < 'abd' AND x LIKE 'abc%'
这样x可以使用索引并且可以缩小LIKE的查询范围。

执行步骤和程序实现如下：
<img src="like.png" />
