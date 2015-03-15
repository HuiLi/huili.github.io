# 函数sqlite3VdbeAllocUnpackedRecord
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;这个函数被用于给UnpackedRecord结构分配一个足够大的内存空间，分配足够大的空间可以被函数sqlite3VdbeRecordUnpack()使用，如果这个函数的第一个形参KeyInfo的结构体pKeyInfo。这个空间的分配也可以使用sqlite3DbMallocRaw()函数或者通过第二个（*pSpace）和第三个形参（szSpace）从未被使用的缓存空间中分配（例如空闲栈空间）。如果按照前者的分配内存空间方式形参*ppFree被设置为一个指针类型变量。这个变量的最后垃圾回由调用者使用sqlite3DbFree函数来回收垃圾。如果内存空间的分配来自于pSpace/szSpace两个参数指定的缓冲空间，*ppFree在程序结束之前被设置为NULL。
如果程序的运行期间发生了OOM异常，会返回NULL。
    关于形参的若干说明：<br>
    KeyInfo *pKeyInfo,              /* 用于描述数据记录 */<br>
    char *pSpace,                   /* 可用于分配的空间 */<br>
    int szSpace,                    /* pSpace中可用于存储的字节数 */<br>
char **ppFree                   /* 程序调用者需要释放这个指针指向的资源 */<br>
