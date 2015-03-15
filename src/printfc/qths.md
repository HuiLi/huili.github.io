# 其他函数
    void sqlite3StrAccumAppend(StrAccum *p, const char *z, int N)

功能：从z到对象StrAccum添加N个文本字节。

    char *sqlite3StrAccumFinish(StrAccum *p)

功能：通过确定以0结尾来结束一个字符串。返回一个指针到生成的字符串。若遇到任何一种错误则返回空指针。

    void sqlite3StrAccumReset(StrAccum *p)

功能：重新设置StrAccum类型的字符串。回收所有被分配的内存。

    void sqlite3StrAccumInit(StrAccum *p, char *zBase, int n, int mx)

功能：初始化一个字符串累加器。

    char *sqlite3VMPrintf(sqlite3 *db, const char *zFormat, va_list ap)

功能：打印到从sqliteMalloc()获得的内存中。使用内部%转换扩展。

    char *sqlite3MPrintf(sqlite3 *db, const char *zFormat, ...)

功能：打印到从sqliteMalloc()获得的内存中。使用内部%转换扩展。

    char *sqlite3MAppendf(sqlite3 *db, char *zStr, const char *zFormat, ...)

和sqlite3MPrintf()相似，但要在格式化字符串之后且返回之前调用参数类型是zStr的sqlite3DbFree()。
这个例程的目的是用于修改现有的字符串。例如：
x = sqlite3MPrintf(db, x, "prefix %s suffix", x);

    char *sqlite3_vmprintf(const char *zFormat, va_list ap)
功能：打印到从sqlite3_malloc()获得的内存中。忽略内部%转换扩展。

    char *sqlite3_mprintf(const char *zFormat, ...)
功能：打印到从sqlite3_malloc()获得的内存中。省略内部%转换扩展。

    char *sqlite3_vsnprintf(int n, char *zBuf, const char *zFormat, va_list ap)
    char *sqlite3_snprintf(int n, char *zBuf, const char *zFormat, ...)

sqlite3_snprintf() 和 snprintf()工作方式很像。区别是：前者省略了当前环境的设置。这对于SQLite来说很重要，因为我们不能使用一个”,“作为小数点来取代在某些环境中已经说明了的小数点“.”。可惜的是:  sqlite3_snprintf()前两个参数执行snprintf()的标准。不幸的是，在不破坏兼容性的情况下改变它已经太迟了。所以我们不得不容忍了这种错误。sqlite3_vsnprintf()是变量参数的版本。

    static void renderLogMsg(int iErrCode, const char *zFormat, va_list ap)
这个例程实际上是用来格式化sqlite3_log()消息的。我们将它包含在一个来自sqlite3_log()的独立例程中，以避免在禁用日志记录时使用堆栈空间。sqlite3_log()必须转化为一个静态缓冲区。它不能动态地分配内存，因为当内存分配器互斥时它会被调用。

    void sqlite3_log(int iErrCode, const char *zFormat, ...)
功能：若启用日志记录，则将格式和消息写入日志。

    void sqlite3DebugPrintf(const char *zFormat, ...)

这是一个能够读懂%lld的printf()版本。用于排除故障。内置于某些windows版本中的printf()无法读懂%lld，并且若给它一个很长的长整型整数则会出现段错误。

    void sqlite3XPrintf(StrAccum *p, const char *zFormat, ...)
功能：装入sqlite3VXPrintf()的可变参数。
