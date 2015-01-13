# 总结
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;通过对OS\_unix.c的分析，了解了UNIX系统对于SQLite的支持。它通过虚拟文件系统实现SQLite对文件的使用与管理。本文件包括Linux，MacOSX，\* BSD，QNX，VxWorks，AIX，HPUX等类Unix操作系统的VFS实现。从实现上来说，它就是通过对文件进行加锁操作来控制文件的访问与并发控制。但我们对于文件中的一些函数的实现细节以及作者的设计方式还不太明白，比如MacOSX。有待进一步的学习和研究。
