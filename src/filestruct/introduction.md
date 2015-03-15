# 文件结构
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;OS\_unix.c是一个与操作系统接口相关的底层文件。它包含包括Linux，MacOSX，\* BSD，QNX，VxWorks，AIX，HPUX等类Unix操作系统的VFS实现。VFS的作用就是采用标准的Unix系统调用读写位于不同物理介质上的不同文件系统，让open()、read()、write()等系统调用不用关心底层的存储介质和文件系统类型。实际上在这个文件中有几种不同的VFS实现，其差异在于文件加锁的方式。默认的实现是使用Posix咨询锁，替代实现使用flock()，dot文件，各种专有锁定模式，或者只是跳过锁定在一起。这个文件的布局如下所示：<br>
——通用的声明和实用功能 <br>
——VxWorks 所使用的唯一的文件ID逻辑<br>
——锁定原语的实现（除了代理锁定）：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Posix Advisory Locks<br>
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;no-op locks<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dot-file locks<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;flock() locking<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;named semaphore locks (VxWorks only)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AFP filesystem locks (MacOSX only)<br>
——和加锁没有关系的一些方法<br>
——对象的定义为所有锁定方法增加了“finder”功能<br>
——sqlite3_vfs方法实现<br>
——proxy uber-locking-method的锁定原语（只在MacOSX上有）<br>
——为所有加锁方法定义了sqlite3\_vfs对象，增加了sqlite3\_os\_init() 和 sqlite3\_os\_end()的实现.
