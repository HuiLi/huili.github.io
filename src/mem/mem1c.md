# mem1.c
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mem1主要在缺省情况下，malloc等函数基础上增加了一个长度的的头。用于统计内存使用量。当使用SQLITE_SYSTEM_MALLOC或没指定宏时时，启用。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;实现中还对这些例程做一层薄的包装以提供一个memsize()函数返回一个现存分配的大小。memsize()也能精确地跟踪未归还内存的字节数，它能确定当一个分配被释放时有多少字节从未归还内存中移除。缺省内存分配器中的memsize()实现是在每个malloc()请求上多分配额外8个字节作为头部，并把分配的大小保存到这个8字节的头部。
