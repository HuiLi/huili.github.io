# ParseContext结构体
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先，本模块定义了一个名为ParseContext（解析上下文）的结构体，此结构体主要存储了标记生成器模块、标记生成器使用的语言ID、自定义名字的fts3表的数组、书写错误信息、嵌套括号的数量、以及一个isNot整型变量，这个变量是用于观察函数getNextNode()调用时’ next node’是否是一元的，是则设为1不是则为0,随后定义了isspace()函数用于判断字符是否为空字符。接下来定义的sqlite3_malloc()函数用于分配内存，成功则返回指针地址，并且清空内存。
