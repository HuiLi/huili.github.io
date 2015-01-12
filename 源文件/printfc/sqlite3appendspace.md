# sqlite3AppendSpace

    void sqlite3AppendSpace(StrAccum *pAccum, int N)
用于增加N个空格字符到指定字符串缓冲区。StrAccum是一个结构体类型，在sqliteInt.h中被定义，定义如下：
![](6.png)
同时，sqlite3AppendSpace调用函数sqlite3StrAccumAppend，以实现：如果缓冲区的空格数少于N，那么添加空格，直到添加N个为止。
