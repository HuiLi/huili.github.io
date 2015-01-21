# 空操作内存分配器（mem0.c）
mem0.c中定义了内存操作的一组函数。同时提供一组空的实现，当使用SQLITE_ZERO_MALLOC宏定义时启用。

        如果SQLite使用SQLITE_ZERO_MALLOC选项来编译，缺省内存分配器将会被忽略，由一个桩内存分配器代替，它不会分配任何内存。任何对桩分配器的调用将返回没有可用内存的报告。这种空操作内存分配器只是作为一个占位符，以便SQLite能链接一些不使用malloc(), free()或realloc()的自定义内存分配器。带SQLITE_ZERO_MALLOC选项编译的应用程序在使用SQLite之前，需要使用sqlite3_config()，结合SQLITE_CONFIG_MALLOC或SQLITE_CONFIG_HEAP来指定新的可选内存分配器。
