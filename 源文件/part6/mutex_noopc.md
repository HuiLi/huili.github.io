# mutex_noop.c


这个文件专门用于测试BUG，包含了所有互斥体实现的基本函数，他只返回SQLITE_OK，不实现具体函数。

此文件定义的互斥体只适用进程调用，通过如下方式适用 sqlite3_config(SQLITE_CONFIG_MUTEX,...)，如果程序开始定义了SQLITE_DEBUG 则使用logic进行错误检查。

