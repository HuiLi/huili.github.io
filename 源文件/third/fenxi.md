# 源码分析
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;为了在 POSIX 和 Win32 之间提供一些可移植性， SQLite 操作系统的接口程序使用一个提取层。 OS提取层的接口程序被定义在 os.h. 每个支持的操作系统有它自己的执行文件： Unix 使用 os_unix.c，windows 使用 os_win.c。每个具体的操作器具有它自己的标题文件： os_unix.h, os_win.h。而我们小组分析的os_win.c是SQLite在Windows平台上的执行接口文件。
