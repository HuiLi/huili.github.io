# 文件函数及结构体分析
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;正如文件的布局所示，文件中定义了一系列的结构体和函数来实现文件的功能。<br>

1.UnixUnusedFd结构体<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 有时，SQLite 关闭文件句柄后不能立即关闭文件描述符。在这种情况下，这个结构的实例用于存储的文件描述符，同时等待机会，要么关闭或重用它。这个结构体包含了要关闭的文件描述符、打开此文件描述符的标志以及同一文件的下一个未使用的文件描述符<br>

2.unixFile结构体<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; unixFile 结构体是sqlite3\_file特定于unix VFS的子类实现。它的结构体原型如下：<br>
typedef struct unixFile unixFile;<br>
struct unixFile {<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3\_io\_methods const \*pMethod;  		/\* 总是第一个进入\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3\_vfs \*pVfs;                   		/\* 创建这个unixFile的VFS \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixInodeInfo \*pInode;              		/\* 关于这个i节点上的锁的消息\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int h;                              	/\* 文件描述符 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned char eFileLock;            		/\* fd（即文件描述符）上加锁的类型 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned short int ctrlFlags;      			/\* 行为位 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int lastErrno;                      		/\* 最后一个输入/输出错误的unix错误 \*/<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; void \*lockingContext;              		/\* Locking style specific state \*/<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; UnixUnusedFd \*pUnused;             	/\* 预分配的UnixUnusedFd \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;const char \*zPath;                 	 	/\* 文件名 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixShm \*pShm;                     	/\* 共享内存段的信息 \*/<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  int szChunk;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#if SQLITE\_ENABLE\_LOCKING\_STYLE
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int openFlags;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#endif<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#if SQLITE\_ENABLE\_LOCKING\_STYLE || defined(\_\_APPLE\_\_)<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned fsFlags;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#endif<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#if OS_VXWORKS<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;struct vxworksFileId *pId;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#endif<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#ifdef SQLITE_DEBUG<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned char transCntrChng;<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned char dbUpdate;<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; unsigned char inNormalWrite;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#endif<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#ifdef SQLITE_TEST<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;char aPadding[32];<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#endif<br>
};

3.posixOpen()函数<br>
不同的 Unix 系统以不同的方式声明 open ()。同样使用open（ const char \*,int mode\_t）。其他的使用open (const char \*,int，......)。当使用指向函数的指针时这些区别是很重要的。为了解决这个问题，文件定义了posixOpen()函数。因为处理这个问题最安全的方式是始终使用这个总是具有相同的定义良好的接口的封装。函数原型如下：<br>
static int posixOpen(const char \*zFile,int flags,int mode)

4.
posixFchown()函数<br>
在某些系统上，来自非根进程对 fchown() 的调用将触发在安全日志中的消息。所以，如果我们不以 root 身份运行，要避免调用 fchown()。函数原型如下：<br>
static int posixFchown(int fd,uid\_t uid,gid\_t gid)

5.
unix_syscall结构体<br>
这个结构体只有三个成员，即系统调用名称、系统调用的当前值以及调用的默认值。可以通过指针函数访问很多的系统调用，因为，他们可能会在运行时被重写。328~436行是一个数组，这个数组保存了一系列有可重写的系统调用的名称和指针。

6.
unixSetSystemCall()函数<br>
这个函数是sqlite3_vfs中实现所有设置系统调用的方法。它的函数原型如下：<br>
static int unixSetSystemCall(<br>
  sqlite3_vfs \*pNotUsed,      /\*VFS 的指针。不使用 \*/<br>
  const char \*zName,       /\* 系统调用重写的名称\*/<br>
  sqlite3\_syscall\_ptr pNewFunc  /\* 指向新的系统调用值的指针\*/<br>
 )<br>
如果成功地更新系统调用指针，返回SQLITE\_OK；如果有是没有名为zName的可配置的系统调用就返回SQLITE\_NOTFOUND。

7.
unixGetSystemCall()函数<br>
设置这个函数是为了返回系统调用的值。其函数原型为：
static sqlite3\_syscall\_ptr unixGetSystemCall(<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3_vfs \*pNotUsed,<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;const char \*zName<br>
)<br>
如果zName不是一个被认可的系统调用名称，返回NULL。如果系统调用目前未定义也返回NULL。

8.
unixNextSystemCall()函数<br>
返回系统调用的值，即在zName后返回的第一次系统调用的名称。如果zName==NULL，则返回第一次系统调用的名称。如果zName是最后一次的系统调用，或者zName不是一个有效的系统调用的名称，则会返回NULL。

9.
robust_open()函数<br>
这个函数增强了open()函数的鲁棒性。它重复调用 open ()多次，直到它不是成功就是因为EINTR以外的某种原因失败(EINTR：linux中函数的返回状态，在不同的函数中意义不同)。<br>
如果文件的创建模式"m"为0，那么将它设置为 SQLite默认的。按系统 umask将默认值修改为SQLITE\_DEFAULT\_FILE\_PERMISSIONS（通常 0644）。如果m不为0，则将文件的创建模式设为完全忽略umask 的 m。只有在创建后缀名为-wal、-journal和-shm 文件时，m 参数将为非零。我们希望这些文件有和他们原始的数据库“完全”相同的权限，纯粹由 umask 设置。在这种方式中，如果数据库文件权限是-rw-rw-rw 或-rw-rw-r-和交易崩溃留下的热日志，则任何能写入到数据库的程序也能恢复热日志。
函数原型如下：<br>
static int robust\_open(const char \*z,int f, mode\_t m)

10.unixEnterMutex()函数<br>
这个函数是为了获得和放弃全局互斥锁的 helper 函数。全局互斥锁用于保护使用此文件中所有的一切都可以由多个线程共享的 unixInodeInfo 和 vxworksFileId 的对象。当有需要时，函数 unixMutexHeld() 用于使用全局互斥锁时所需的assert() 函数。此函数仅用作 assert() 语句的一部分：<br>
static void unixEnterMutex(void)

11.azFileLock()函数<br>
这个函数打印来自调试二进制文件的追踪信息。它的返回值是提供的整型的加锁类型的字符串表示形式，即文件的加锁类型：NONE、SHARED、RESERVED、PENDING、EXCLUSIVE。函数原型为：<br>
static const char \*azFileLock(int eFileLock)

12.lockTrace()函数<br>
这个函数是为了打印出所有加锁操作的信息。当存在多线程的时候，如果没有进行排查就进行加锁，就会出现操作错误。而这个程序就是用于在多线程的平台上的疑难解答锁。其函数原型如下：<br>
static int lockTrace(int fd,int op,struct flock *p)

13.sqliteErrorFromPosixError()<br>
这个函数将标准的POSIX错误代码转化成为了sqlite3的客户端有用的东西。具体来说，它将sqlite3中各种错误代码翻译成为了客户端能看懂的提示。如将各种"重试"错误转换成为为SQLITE\_BUSY 、各种"请立即关闭文件描述符"错误转换为SQLITE\_IOERR，这样客户端很容易就看懂了发生的错误。锁或文件系统支持的锁的初始化期间发生的错误应分开成ENOLCK，ENOTSUP，EOPNOTSUPP来处理。其函数原型如下：<br>
static int sqliteErrorFromPosixError(int posixError,int sqliteIOErr)

14.vxworksFileId 结构体<br>
这个文件提供了VxWorks的VFS的实现，但是对于VxWorks而言，它有自己独特的文件ID命名，用来提高它的性能。在大多数版本的Unix中，我们可以通过串联设备数量和i节点数量得到一个唯一的文件ID，但这不能用在VxWorks上。在VxWorks上，一个唯一的文件 ID必须基于规范的文件名。一个指向这个结构体的一个实例指针可以用作 VxWorks 中的一个唯一的文件ID。这种结构体的每个实例包含一份规范的文件名。还有一个引用计数。当指针指向它的数目降到零时回收结构体。有很多文件从来不会打开一次，查找不是性能关键的路径，所以它足以将这些结构放在一个链表上。这个结构体如下所示：<br>
struct vxworksFileId {<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; struct vxworksFileId \*pNext;  		/\* 指向链表中的下一个 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int nRef;                   	/\* 对这一引用的次数\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int nName;                 		/\* zCanonicalName[]字符串的长度 \*/<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; char \*zCanonicalName;         	/\* 规范的文件名 \*/<br>
};

15.vxworksSimplifyName()<br>
这个函数是对VxWorks中文件命进行规范化，函数的主题主要是一些循环，查找不规范的位置并进行修改。通过进行以下更改可以简化一个文件名为其规范的形式<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;删除任何尾部的和重复的 /<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;将/./ 转换为 /<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;将/A /../ (A 只是任何简单的名称)转换成 /<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对文件ID就地进行更改，返回新的名称长度。原来的文件名在z[0..n-1]中。返回简化的名称的字符的数目。
函数的原型如下：<br>
static int vxworksSimplifyName(char \*z,int n)

16.vxworksFindFileId()<br>
这个函数用于查找唯一的一个给定绝对路径名的文件ID。它的返回值是一个vxworksFileId 对象的指针。这个指针是唯一的文件ID。在返回对象之前VxworksFileId 对象的 nRef 字段是递增的。如有必要，可以创建一个新的 vxworksFileId 对象并将其添加到全局列表。但如果如果出现内存分配错误，则返回 NULL。函数原型如下：
static struct vxworksFileId \*vxworksFindFileId(const char \*zAbsoluteName)

17.unixFileId结构体<br>
这个结构体的成员包括设备号、VXWORKS的特有的文件ID以及I节点数目。这个结构体的一个实例用于查找一个特定的 unixInodeInfo 对象的键。如果是在VxWORKS中则需要使用它自己定义的标准的文件ID。

18.unixInodeInfo结构体<br>
这个结构体的一个实例分配给每个打开的i节点。或者，在Linux线程中，这些结构体中有一个是给由线程打开的i节点的。单一的i节点可以有多个文件描述符，因此每个 unixFile 结构包含一个指针，指向该对象的一个实例，此对象保持 unixFile 指向它的数目的计数。<br>
struct unixInodeInfo {<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;struct unixFileId fileId;       				/\* 查找键 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int nShared;                    			/\* 持有共享锁的数目\*/<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned char eFileLock;<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned char bProcessLock;          			 /\*独占进程锁 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int nRef;                      			 /\* 指向这个结构体的指针数目\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixShmNode \*pShmNode;         		 /\* 与这个i节点有关的共享内存 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int nLock;                     			 /\* 未完成的文件锁定的数目 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;UnixUnusedFd  \*pUnused;        		 /\* 关闭未使用的文件描述符 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixInodeInfo \*pNext;           			 /\* 所有的 unixInodeInfo 对象的列表 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixInodeInfo \*pPrev;           			 /\* 双重链接 \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#if SQLITE\_ENABLE\_LOCKING\_STYLE<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned long long sharedByte;  			 /\* 用于模拟APF共享锁 \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#endif<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#if OS_VXWORKS<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sem_t \*pSem;                    		/\* 命名的 POSIX 信号量 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;char aSemName[MAX_PATHNAME+2];  	/\* 该信号量的名称 \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#endif<br>
};

19.unixLogErrorAtLine()函数<br>
这个unixLogError\_x()函数永远只能通过宏定义的unixLogError()函数调用。在操作系统函数出现错误并且错误被设定之后调用它。它使用sqlite3\_log()记录一条消息，消息包含当前errno的值，如果可能，认可读的等效来自于strerror() or strerror\_r()传递给宏的第一个参数应是将被返回到SQLite的（例如SQLITE\_IOERR\_DELETE，SQLITE\_CANTOPEN）错误代码。随后的两个参数应该是失败的操作系统函数名称（例如"取消链接"，"打开"），以及相关的系统文件路径。函数原型如下：<br>
static int unixLogErrorAtLine(int errcode,const char *zFunc, const char *zPath,int iLine )
参数：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;errcode：SQLite错误代码<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;zFunc：失败的操作系统函数名称<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;zPath：错误的文件路径关联<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;iLine：发生错误的位置的源代码行号

20.robust_close()函数<br>
这个函数的功能是关闭一个文件描述符，它也是增加了close()的鲁棒性。因为只在一个非常恶心的应用程序中或在一个非常恶心的平台中，它才可能会失败。我们假设close()几乎总是工作的。如果它失败了，除了记录错误之外，仅仅泄漏文件描述符。需要注意的是，由于文件描述符可能已经被另一个线程重新使用，在EINTR后重试close（）是不安全的。所以，我们甚至不尝试从EINTR恢复。只要记录错误然后继续执行。函数原型如下：<br>
static void robust_close(unixFile \*pFile,int h,int lineno)

21.closePendingFds()函数<br>
用来关闭所有存放在unixInodeInfo->pUnused列表中的文件描述符

22.releaseInodeInfo()函数<br>
函数的功能是释放一个i节点，但是在释放之前findInodeInfo()分配一个unixInodeInfo结构。当这个函数被调用时，必须使用unixEnterMutex()函数输入的互斥量。函数原型如下：<br>
static void releaseInodeInfo(unixFile *pFile)

23.findInodeInfo()函数<br>
给定一个文件描述符，来定位unixInodeInfo对象描述文件描述符。在必要时可以创建一个新的。如果出现错误，返回值可能是未初始化的。当这个函数被调用时，必须使用unixEnterMutex()函数输入的互斥量，返回相应的错误代码。
函数原型如下：<br>
static int findInodeInfo( unixFile \*pFile,unixInodeInfo **ppInode )<br>
在OS x系统的msdos文件系统中，对于大小为0的文件，i节点会报错。为了解决这个问题(这是OS X系统的一个bug，而不是SQLite的问题)，我们将写入一个字节到i节点中使得文件大小增加1.这个字节写入一个ASCII 字符S，在每个SQLite数据库中文件头的第一个字节也会这样处理。通过这种方式，如果出现多个进程进行竞争，如果其中一个进程已经占用了数据库的第一个页面，其他进程就会识别而不会对数据库造成破坏。代码行1223~1265就是为了实现这个功能。

24.unixCheckReservedLock()函数<br>
这个函数用来检查这个进程或其他进程指定的文件是否持有保留锁。如果持有这样的锁，设置\*pResOut为非零值，否则设置为0.返回值设置为SQLITE\_OK，除非在锁定检查期间出现I/O错误。其函数原型如下：<br>
static int unixCheckReservedLock(sqlite3_file \*id,int \*pResOut)

25.unixFileLock()函数<br>
这个函数试图在文件pFile设置一个系统锁，这个锁由pLock描述。如果pFile是从unix-excl打开读/写的，那么只有曾经获得的排它锁，这是第一次获得试图得到的任何的锁。所有后续系统锁定操作成为空操作。锁定操作仍然发生在内部，以便协调在这一进程内的单独的数据库连接之间的访问，但这一切都是在内存中处理，操作系统并不参与。这个函数传递到fcntl(F_SETLK)，如果pFile使用了除了"unix-excl"之外的任何VFS，或者pFile 在"unix-excl"上是打开的并且是只读的。如果调用成功完成，返回零，或-1，如果调用fcntl()失败。在这种情况下，errno是设置正确(通过fcntl())。函数原型如下：<br>
static int unixFileLock(unixFile *pFile,struct flock *pLock)

26.unixLock()函数<br>
这个函数实现对文件进行加锁。函数的原型如下：
static int unixLock(sqlite3_file *id，int eFileLock)<br>
这个程序只会增加锁，需要使用sqlite3OsUnlock()程序以降低锁定级别。函数中描述了各种锁的执行和锁在POSIX咨询共享锁和排它锁之间转换的原语(以下称为读锁和写锁，以避免混淆SQLite的锁名称)。在os.h中定义的符号识别 'pending byte' 和 'reserved byte'，每个字节在众所周知的偏移处，'shared byte range'在这个偏移处的510个字节范围内。为了获得共享锁，要在'pending byte'获得读锁。如果成功了，从'shared byte range' 得到的一个随机字节读锁定，并且'pending byte' 上的锁移除。在有一个共享锁后，一个进程可能只能获得保留的锁。通过获取'reserved byte'上的写锁执行这个保留锁。在已经获得了共享锁后，一个进程可能只获得一个未决锁。通过获得'pending byte'的写锁执行这个未决锁。这确保了不能获得新的共享锁，但现有的共享锁还是允许继续下去。一个进程在获取未决锁的过程中不必获取共享锁。该算法使用此属性在崩溃后回滚日志文件。在持有未决锁后获得的排它锁通过获得整个'shared byte range'上的写锁实现。从之所有其他锁后需要在这个范围内的字节上有读锁，这可以确保数据库上没有其他锁。单个的字节不能代替'shared byte range'的原因是很多Windows版本不支持读锁。通过锁定一个范围内的一个随机字节，可能会存在并发共享，即使锁定原语使用的一直是一个写锁。<br>
在加锁的时候需要注意的是确保锁的序列是正确的：不要从未加锁移动到高于共享锁的状态；SQLite从未明确要求pendig锁；当请求一个保留锁时，共享锁总是持有的。

27.setPendingFd()函数<br>
这个函数是将文件句柄使用的文件描述符添加到相应的列表。

28.posixUnlock()函数<br>
这个函数用来降低文件描述符上的锁定级别为eFileLock参数指定的。eFileLock必须是NO\_LOCK或SHARED\_LOCK。如果文件描述符的锁定级别已经达到或低于所请求的锁定级别，此操作是一个空操作。如果handleNFSUnlock为真，则降低一个EXCLUSIVE_LOCK为共享锁，其字节范围分为两部分，一部分是为加锁然后设置为读锁，另一部分则是简单的未加锁。这一错误存在DSB NFS 加锁（在MacOSX 10.3+中也见过），即当设置了一个读锁就无法在该区域删除写锁。在这个操作过程中当减少锁以便其他进程可以再次读取数据库文件的时候，要确保事务计数器已经更新，如果数据库文件文件的任何部分发生了改变。如果事务计数器没有更新，对同一文件的其他连接可能没有意识到该文件已经更改，因此可能不知道要刷新其缓存。在NFS上降级到共享锁，包括在建立读锁之前需要清除写锁，以免在降低2块锁时有竞争的情况，使一个写锁覆盖部分范围，直到读锁覆盖剩下的部分：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1:   [WWWWW]<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2:   [....W]<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3:   [RRRRW]<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4:   [RRRR.]<br>
函数原型如下：<br>
static int posixUnlock(sqlite3_file *id,int eFileLock,int handleNFSUnlock)

29.closeUnixFile()函数<br>
这个函数执行所有文件锁定计划共同的“关闭文件”操作部分。关闭目录和文件句柄，如果他们是有效的，并设置unixFile结构的所有字段为0。在调用这个程序时它并不需要互斥量，即使是在VxWorks上。在VxWorks上，互斥量通过vxworksReleaseFileId()例程获得。<br>
static int closeUnixFile(sqlite3_file *id)

30.unixClose()函数<br>
这个函数的功能是关闭一个文件。 如果一个文件上有未完成的锁，则不能关闭这个文件，因为那样会清除这些锁。相反，将该文件描述符添加到pInode->pUnused列表。当清除最后的锁定时，这个文件会自动关闭。<br>
static int unixClose(sqlite3_file *id)

31.
seekAndRead()函数<br>
函数原型：static int seekAndRead(unixFile *id, sqlite3_int64 offset, void *pBuf, int cnt)<br>
输入：<br>
id:文件指针<br>
offset:文件偏移量<br>
pBuf:内存指针<br>
cnt:数量<br>
输出 ：返回实际读取的字节数<br>
作用：从id指定的文件的offset偏移量处开始读取cnt个字节到内存pBuf处

32.
unixRead()函数<br>
函数原型：static int unixRead(sqlite3\_file \*id, void \*pBuf, int amt, sqlite3\_int64 offset)<br>
输入：<br>
id:文件指针<br>
pBuf:内存指针<br>
amt:读取字节数<br>
offset:文件偏移量<br>
输出 ：返回调用状态结果码<br>
作用：从id指定的文件的offset偏移量处开始读取amt个字节到内存pBuf处
直接调用seekAndRead函数，实际是对seekAndRead函数的封装

33.
seekAndWrite()函数<br>
函数原型：static int seekAndWrite(unixFile *id, i64 offset, const void *pBuf, int cnt)<br>
输入：<br>
id:文件指针<br>
offset:文件偏移量<br>
pBuf:内存指针<br>
cnt:写入字节数<br>
输出 ：返回实际写入的字节数<br>
作用：从pBuf处将cnt个字节写入文件id的offset偏移量处

34.
unixWrite()函数<br>
函数原型：static int unixWrite(sqlite3\_file \*id, const void \*pBuf, int amt,sqlite3\_int64 offset )<br>
输入：<br>
id：文件指针<br>
pBuf：内存指针<br>
amt：写入字节数<br>
offset：文件偏移量<br>
输出 ：返回写操作结果<br>
作用：从pBuf处将cnt个字节写入文件id的offset偏移量处
实际会调用seekAndWrite进行写操作

35.
full_fsync()函数<br>
函数原型：static int full_fsync(int fd, int fullSync, int dataOnly)<br>
作用：提供更好用的同步函数，根据不同情况调用osFcntl或者fsync或者fdatasync。

36.
openDirectory()函数<br>
函数原型：static int openDirectory(const char *zFilename, int *pFd)<br>
作用：打开一个包含指定文件的目录，即文件所在的目录
说明：打开一个文件描述符到包含文件zFilename的目录。如果成功，\*pFd设置为打开的文件描述符，SQLITE\_OK返回。如果出现错误，SQLITE\_NOMEM或者SQLITE\_CANTOPEN返回，\*pFd设置为一个未定义的值。如果SQLITE_OK返回,调用者负责关闭文件描述符* pFd，使用close()。

37.
unixSync()函数<br>
函数原型：static int unixSync(sqlite3_file *id, int flags)
作用：数据同步<br>
说明：确保所有的写入到一个特定文件提交到磁盘。如果dataOnly==0,那么文件本身及其元数据(文件大小、访问时间等)是同步的。如果dataOnly != 0，那么只有文件数据是同步的。在Unix中,也确保文件的目录条目已经通过同步包含文件的目录创建。如果我们不这样做,我们遇到停电，日志目录条目在我们重启后可能不存在。下一个SQLite访问文件将不知道日志存在（因为日志目录条目从未创建），事务不将回滚，可能导致数据库毁坏。

38.
unixTruncate()函数<br>
函数原型：static int unixTruncate(sqlite3_file *id, i64 nByte)<br>
作用：截断一个打开的文件到指定的大小。<br>
说明：如果用户已经配置了这个文件的块大小，,截断文件以便于包含整数的块（即操作之后的实际文件大小可能大于请求的大小）。如果我们做一个正常写入到数据库文件（而不是做一个热日志回滚或者写入到一些不是正常数据库文件的文件），我们截断文件长度为零，这有效的更新改变计数器。这可能发生，当从一个0长度的源使用备份API来恢复数据库。

39.
unixFileSize()函数<br>
函数原型：static int unixFileSize(sqlite3_file *id, i64 *pSize)<br>
作用：确定当前文件的大小,单位为字节<br>
说明：调用这个函数来处理SQLITE\_FCNTL\_SIZE\_HIN文件控制操作。扩大数据库到nBytes大小（算到下一个块大小）。如果数据库已经nBytes或者更大，这个程序是一个空操作。

40.
fcntlSizeHint()函数<br>
函数原型：static int fcntlSizeHint(unixFile *pFile, i64 nByte)<br>
作用：扩充数据库文件大小至nByte,并自动调整为块的整数倍

41.
unixModeBit()函数<br>
函数原型：static void unixModeBit(unixFile \*pFile, unsigned char mask, int \*pArg)<br>
说明：如果\*pArg初始是负的，那么这是一个查询。设置\*pArg为1或者0取决于是否pFile->ctrlFlags位屏蔽被设置。如果\*pArg为0或者1，那么清除或者设置pFile->ctrlFlags屏蔽位。

42.
unixFileControl()函数<br>
函数原型：static int unixFileControl(sqlite3_file *id, int op, void *pArg)<br>
作用：有关一个打开文件句柄的信息和控制。使用一系列case语句，实现不同的控制操作。

43.
unixSectorSize()函数<br>
函数原型：static int unixSectorSize(sqlite3_file *pFile)<br>
作用：以指定文件的底层块设备字节来返回扇区大小。这几乎总是512字节，但可能对于一些设备来说更大。 SQLite代码假设这个函数不会失败。它还假设如果两个文件在相同的文件系统目录中创建（即一个数据库和它的日志文件）两个扇区大小将一样。

44.
unixDeviceCharacteristics()函数<br>
函数原型：static int unixDeviceCharacteristics(sqlite3_file *id)<br>
作用：返回文件的设备特征。<br>
说明：这个虚拟文件系统的用来默认返回POWERSAFE_OVERWRITE。<br>
但是，这个选择是矛盾的，因为技术上底层文件系统不会总是提供电源安全覆盖。
（换句话说，发生停电事件后，部分从来没被写入的文件可能最终被修改。）但是，non-PSOW行为，是非常非常罕见的。并且声明PSOW使日志的I/O请求数量大量减少，因为很多填充被消除。因此，虽然POWERSAFE_OVERWRITE在默认情况下，但有个文件控制可用来关掉它且URI查询参数可用来关掉它。

45.
unixShmNode和unixShm结构体<br>
struct unixShmNode {<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixInodeInfo \*pInode;       /\*unixInodeInfo拥有这个SHM节点\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3_mutex \*mutex;      /\* 互斥访问这个对象\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;char \*zFilename;           /\* mmapped文件名 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int h;                     /\* 打开文件描述符\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int szRegion;              /\*共享内存区域的大小\*/<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u16 nRegion;               /\* 数组apRegion大小\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u8 isReadonly;             /\*如果只读则为真 \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;char \*\*apRegion;           /\*映射共享内存区域的数组\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int nRef;                  /\*许多unixShm对象指向这一点\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixShm \*pFirst;           /\*所有unixShm对象指向这个 \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#ifdef SQLITE_DEBUG
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u8 exclMask;               /\* 持有排它锁掩码\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u8 sharedMask;             /\* 持有共享锁的掩码\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u8 nextShmId;              /\*下一个可用的unixShm.id值 \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\#endif<br>
};<br>
说明：对象用来代表一个共享内存缓冲区。当多个线程都引用相同的wal-index索引，每个线程有它自己的unixShm对象，但是它们都指向这个unixShmNode对象的一个实例。换句话说，每个wal-index每个过程只打开一次。<br>
每个unixShmNode对象关联到一个unixInodeInfo对象。我们可以合并这个对象到unixInodeInfo，但这意味着每一个打开的文件不使用共享内存（大多数打开的文件）必须携带这额外的信息。所以这个unixInodeInfo对象包含这个unixShmNode对象的一个指针，且unixShmNode对象仅当需要的时候创建。当创建或者销毁这个对象或当读或写字段nRef时，unixMutexHeld()必须为真。<br>
fid，zFilename字段在对象创建之后是只读的。 要么unixShmNode.mutex互斥必须被持有或unixShmNode.nRef为0，并且当读或写在这个结构体的任何其他字段时unixMutexHeld()为真。<br>
struct unixShm {<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixShmNode \*pShmNode;     /\*底层unixShmNode对象\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixShm \*pNext;            /\*下一个一样unixShmNode的unixShm\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u8 hasMutex;               /\*如果持有unixShmNode互斥则为真\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u8 id;                     /\*unixShmNode内连接的Id\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u16 sharedMask;            /\*持有共享锁掩码\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;u16 exclMask;              /\*持有排它锁掩码\*/<br>
};<br>
说明：这个虚拟文件系统使用内部的结构体来记录一个打开的共享内存连接的状态。unixShm.pFile，unixShm.id字段当这个对象创建时被初始化，且之后是只读的。所以其他字段是读/写。unixShm.pFile->mutex必须被持有，当访问任何读/写字段。

46.
unixShmSystemLock()函数<br>
函数原型：static int unixShmSystemLock(<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixShmNode \*pShmNode,   /\*应用锁打开共享内存段\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int lockType,              /\* F\_UNLCK, F\_RDLCK, or F\_WRLCK \*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int ofst,             /\*锁定范围的第一个字节\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int n                  /\* 锁定的字节数\*/<br>
)<br>
说明：对从ofst通过ofst+n-1的所有字节，应用posix咨询锁。<br>
如果掩码是UNIX\_SHM\_C，则锁定数据块，否则不锁定

47.
unixShmPurge()函数<br>
函数原型：static void unixShmPurge(unixFile *pFd)<br>
作用：清除所有unixShmNode.nRef为0的条目的unixShmNodeList列表。<br>
这不是一个VFS共享内存方法，这是一个VFS共享内存方法的通用函数调用。

48.unixOpenSharedMemory()函数<br>
函数原型：static int unixOpenSharedMemory(unixFile *pDbFd)<br>
作用：打开一个与打开的数据库文件pDbFd有关的共享内存区域。数据库进程之间共享，多个数据库进程共同访问数据库文件的时候就需要共享内存<br>
说明：用于实现共享内存的该文件是和打开的数据库文件在同一个目录，并且和带"-shm"后缀的打开的数据库文件有相同的名字。例如，如果数据库文件是"/home/user1/config.db"，那么这个为共享内存创建和映射的文件将被称为"/home/user1/config.db-shm"。<br>
另一种方法是使用在/dev/shm或/dev/tmp下的文件，或者其他一些tmpfs接口。<br>
但是如果在数据库文件的不同目录的一个文件被使用，那么不同的访问权限或者一个chroot()可能会导致两个在同一个数据库的不同进程，使用不同共享内存文件结束。意味着他们的内存不会被真的共享，导致数据库毁坏。然而，使用-DSQLITE_SHM_DIRECTORY="/dev/shm"或等价条件，这个tmpfs文件用法能在编译阶段被启用。SQLITE_SHM_DIRECTORY编译阶段选项的使用，导致SQLite不兼容的构建；不同SQLITE_SHM_DIRECTORY设置的SQLite的构建试图同时使用相同的数据库文件，很可能产生数据库毁坏。SQLITE_SHM_DIRECTORY编译阶段选项被认为是“不支持”，并且可能会在未来的SQLite发布中消失。<br>
当打开一个新的共享内存文件，如果在这一过程或其他过程没有该文件的其他用例是当前打开的，那么这个文件必须被截断为0长度或清除其头部。<br>
如果原始数据库文件(pDbFd)是使用“unix-excl”VFS，这意味着在数据库文件持有排它锁，并且没有其他进程读或写数据库。在这种情况下，我们不用真正的需要共享内存。没有共享内存文件被创建。共享内存将被堆内存模拟。

49.
unixShmMap()函数<br>
函数原型：static int unixShmMap(<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3\_file \*fd,               /\* 处理打开数据库文件\*/
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int iRegion,                    /\* 检索区域\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int szRegion,                   /\* 区域的大小\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int bExtend,                    /\* 为真则去扩展文件如果有必要\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;void volatile \*\*pp              /\* 内存映射\*/<br>
)<br>
说明：调用这个函数来获得与数据库文件fd有关的共享内存区域iRegion指针。共享内存区域从0开始编号。每一个共享内存区域大小是szRegion字节。<br>
如果发生一个错误，返回一个错误代码并且*pp设置为NULL。<br>
否则，如果bExtend参数是0且请求的共享内存区域没有被分配（通过任何客户端，包括一个运行在单独的进程），然后*pp设为NULL，SQLITE_OK返回。如果bExtend非零且请求的共享内存区域还没有被分配，它被这个函数分配。<br>
如果共享内存区域已经被分配或者被这个如上所述的调用分配，那么它映射到这个进程地址空间（如果它还没有），*pp被设置来指向映射的内存且SQLITE_OK返回。

50.
unixShmLock()函数<br>
函数原型：static int unixShmLock(<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3_file \*fd,         /\* 持有共享内存的数据库文件\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int ofst,                 /\* 第一个获得或释放锁\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int n,                      /\* 获取或释放的锁的数量\*/<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int flags                  /\* 如何处理锁\*/<br>
)<br>
作用：改变一个共享内存段的锁状态。<br>
共享锁和排它锁的关系在这里有点和在posix不同。在xShmLock()，一个对象能够从解锁到共享和返回或者从解锁到独占和返回。但是一个对象可能不会从共享到独占或者从独占到共享。

51.
sqlite3\_io\_methods对象定义<br>
\#define IOMETHODS(FINDER, METHOD, VERSION, CLOSE, LOCK, UNLOCK, CKLOCK)<br>
说明：这一部分包含sqlite3\_io\_methods对象定义，提出声明I / O方法。其是实现各种文件锁定策略。它也包含“探测”函数的定义。一个探测函数被用于为一个特定数据库文件定位适当的sqlite3\_io\_methods对象。the sqlite3_vfs VFS对象的pAppData字段被初始化来为这个VFS指向正确的探测函数。<br>
大多探测函数返回一个指向固定sqlite3\_io\_methods对象的指针。唯一有趣的探测函数是autolockIoFinder，它查看文件系统类型并试图从其推测出最好的锁策略。为探测函数F，创建了两个对象：<br>
(1) 真正的探测函数命名为FImpt()。<br>
(2) 指向这个函数的常量指针命名为"F"<br>
F指针的指针作为VFS对象的pAppData值<br>
每一个该宏的实例生成两个对象：一个常量sqlite3\_io\_methods对象调用有关闭，加锁，解锁，CKRESLOCK锁方法的方法。一个I/O方法探测函数成为FINDER，其返回一个在之前bullet的指向METHOD对象的指针。

52.
autolockIoFinderImpl()函数<br>
函数原型：static const sqlite3\_io\_methods *autolockIoFinderImpl(<br>
  const char \*filePath,    /\* 数据库文件名称\*/<br>
  unixFile \*pNew           /\* 数据库文件的打开文件对象\*/<br>
)<br>
说明：这种“探测”函数试图为数据库文件"filePath"，确定最佳锁定策略。然后它返回实现这一策略的sqlite3\_io\_methods对象。这仅供MacOSX。

53.
fillInUnixFile()函数<br>
函数原型：static int fillInUnixFile(<br>
  sqlite3_vfs \*pVfs,      /\*  指向vfs对象的指针 \*/<br>
  int h,                  /\*  被打开文件的打开文件描述器\*/<br>
  sqlite3_file \*pId,      /\*  在这里写入unixFile结构\*/<br>
  const char \*zFilename,  /\*  正在被打开文件的文件名\*/<br>
  int ctrlFlags           /\*  零或更多UNIXFILE\_\*值\*/<br>
)<br>
作用：初始化被pId指向的unixFile的内容。

54.
unixTempFileDir()函数<br>
函数原型：static const char *unixTempFileDir(void)
作用：返回放置临时文件的路径名。如果没有合适的临时文件路径名被找到，返回NULL。

55.
unixGetTempname()函数<br>
函数原型：static int unixGetTempname(int nBuf, char *zBuf)<br>
说明：在zBuf创建临时文件名。zBuf必须通过调用程序被分配，而且必须足够大去承载至少和一样的pVfs->mxPathname字节数。

56.
findReusableFd()函数<br>
函数原型：static UnixUnusedFd *findReusableFd(const char *zPath, int flags)<br>
作用：搜索一个未被使用在数据库文件中被打开的文件描述符（不是日志文件或主日志文件）,这个数据库文件通过带有SQLITE_OPEN_XXX标志的路径名zPath，和在这个函数中传入的第二个参数相匹配。这样一个文件描述符可以如果数据库连接被关闭，但相关的文件描述符不能被关闭，因为在同一个文件打开其它的文件描述符是持有file-lock。在unixclose()功能的评论和描述"冗长的评论存在POSIX咨询锁定"在进一步的细节，该文件的开始。如果合适的文件描述符被找到，然后将它返回。如果描述符被设置，－1被返回。

57.
findCreateFileMode()函数<br>
函数原型：static int findCreateFileMode(<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;const char *zPath,              //正在被创建文件(可能)的路径<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int flags,                     //传入xOpen()的第四个参数的标记<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mode_t *pMode,                  //打开文件的许可<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;uid_t *pUid,                   //设置到此文件的uid<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;gid_t *pGid                     //设置到此文件的gid<br>
)<br>
说明：这个方法被unixOpen()调用，用来决定unix创建新文件时的许可。如果没有错误发生，则SQLITE\_OK被返回，并且一个适合的值传入open(2)的第三个参数被写到\*pMode。如果一个IO错误发生，一个SQLite错误代码被返回并且\*pMode的值不会被修改在大多数的情况下，这个历程会设置\*pMode为0，这个将会成为使用SQLITE\_DEFAULT\_FILE\_PERMISSIONS适应umask的robust\_open()的迹象。但是如果被打开的文件是一个WAL或者通常的日志文件，则这个方法问询文件系统的许可在相应的数据库文件，并且设置*pMode为这个值。不论何时可能，WAL和日志文件被创建使用相同的许可像相关联的数据库文件。如果SQLITE\_ENABLE\_8\_3\_NAMES选项可用，则这个原始文件名将不可用。但是8\_3\_NAMES只被用作FAT文件系统并且许可在这里没关系，所以紧紧使用默认许可。<br>
zPath是一个指向WAL的路径或者一个日志文件。接下来的块源自从zPath相关联数据库。这个块持有如下的命名规定<br>
       "path to db-journal"<br>
       "path to db-wal"<br>
       "path to db-journalNN"<br>
       "path to db-walNN"<br>
NN是一个小数。NN命名模式使用test_multiplex.c模块。

58.
unixOpen()函数<br>
函数原型：static int unixOpen(<br>
  sqlite3_vfs \*pVfs,          //xOpen方法的VFS<br>
  const char \*zPath,         //被打开文件的路径名<br>
  sqlite3_file \*pFile,         //被装满的文件描述符<br>
  int flags,                 //控制打开的输入标记<br>
  int \*pOutFlags            //返回SQLite内核的输出标记<br>
)<br>
作用：打开文件zPath<br>
在此前，SQLite操作系统层使用三个函数在这里<br>
     sqlite3OsOpenReadWrite();<br>
     sqlite3OsOpenReadOnly();<br>
     sqlite3OsOpenExclusive();<br>
这些调用和下面的混合体的标志相符合<br>
     ReadWrite() ->     (READWRITE | CREATE)<br>
     ReadOnly()  ->     (READONLY)<br>
     OpenExclusive() -> (READWRITE | CREATE | EXCLUSIVE)<br>
老的OpenExclusive()接受一个布尔变量- "delFlag"。如果为真，这个文件被自动配置为当文件句柄关闭时删除。为了在使用新的接口达到相同的效果，添加DELETEONCLOSE标记在以上对OpenExclusive()具体说明OpenExclusive()。<br>
如果创建一个主的或主文件日志，这个方法也会打开一个在本目录的文件描述符。第一次unixSync()被调用，这个目录文件描述符将被fsync()或close()d。如果变量zPath是一个空指针，这个方法被要求去打开一个临时文件。使用这个缓冲区存储文件名。<br>
如果这个进程被当作启动运行，并且如果创建一个新的或调日志或WAL文件，设置日志或者WAL的所有权作为和原数据库相同的。

59.
unixDelete()函数<br>
函数原型：static int unixDelete(<br>
  sqlite3_vfs *NotUsed,     //VFS包含这点作为xDelete方法<br>
  const char *zPath,        //要被删除的文件名<br>
  int dirSync              //如果为真，在删除文件后fsync()目录<br>
)<br>
作用：在zPath删除文件。如果dirSync参数为真，删除此文件后fsync()这个目录。

60.
unixAccess()函数<br>
函数原型：static int unixAccess(<br>
  sqlite3_vfs *NotUsed,      //VFS包含xAccess方法<br>
  const char *zPath,         //要检查文件的路径<br>
  int flags,                 //我们将从zPath文件得到什么？<br>
  int *pResOut             //自这里写布尔结果<br>
)<br>
作用：测试存取文件zPath权限的存在性。测试的表现以标记值为依据。否则返回0。

61.
unixFullPathname()函数<br>
函数原型：static int unixFullPathname(<br>
  sqlite3_vfs \*pVfs,            //指向vfs对象的指针<br>
  const char \*zPath,           //可能的输入相对路径<br>
  int nOut,                   //输出缓冲区的自己数<br>
  char \*zOut                 //输出缓冲区<br>
)<br>
作用：将一个相对路径转换为绝对路径。这个相对路径在zPath指向的缓冲区内被存储为nul-terminated字符串。zOut指向的缓冲区至少有sqlite3\_vfs.mxPathname字节(在这种情况下，为MAX\_PATHNAME字节数)。返回前绝对路径被写到缓冲区。

62.
unixDlOpen()函数<br>
函数原型：static void *unixDlOpen(sqlite3_vfs *NotUsed, const char *zFilename)<br>
作用：打开共享库的接口，寻找共享库的入口点，并且关闭共享库。

63.
unixRandomness()函数<br>
函数原型：static int unixRandomness(sqlite3_vfs *NotUsed, int nBuf, char *zBuf)<br>
作用：将nBuf字节的随机数据写入被支持的zBuf缓冲。

64.
unixSleep()函数<br>
函数原型：static int unixSleep(sqlite3_vfs *NotUsed, int microseconds)<br>
作用：休眠一会儿。返回睡眠时间。这个我们想去休眠的参数是以微秒计算。返回值是从底层操作系统请求的微秒数。这个数值可能大于或等于这个参数，但是不会小于此参数。

65.
unixCurrentTimeInt64()函数<br>
函数原型：static int unixCurrentTimeInt64(sqlite3\_vfs \*NotUsed, sqlite3\_int64 \*piNow)<br>
作用：查询当前时间(在通用坐标系时间)。写入*piNow当前时间和日期作为一个Julian Day次数86\_400\_000。如果成功，返回SQLITE\_OK。如果时间和日期不能被找到则返回SQLITE_ERROR。

66.
unixCurrentTime()函数<br>
函数原型：static int unixCurrentTime(sqlite3_vfs \*NotUsed, double \*prNow)<br>
作用：查找当前时间（世界标准时间）。写当前的时间和日期作为儒略日数为\* prNow并返回0返回1，如果时间和日期不能被发现。

67.
unixGetLastError()函数<br>
函数原型：static int unixGetLastError(sqlite3\_vfs \*NotUsed, int NotUsed2, char \*NotUsed3)<br>
说明：我们与提供更好的低级错误信息的打算加入xGetLastError（）方法时，运行系统的问题上来。在SQLite的操作。但到目前为止，没有任何已经在内核中实现。因此，这个程序不会被调用。就目前而言，它仅仅是一个占位符。

68.
proxyGetLockPath()函数<br>
函数原型：static int proxyGetLockPath(const char \*dbPath, char \*lPath, size\_t maxLen)<br>
作用：在dbPath数据库所属的代理锁文件路径被写入到lPath，它必须指向有效，可写入的足够大的内存为maxLen长度的文件路径

69.
proxyCreateLockPath()函数<br>
函数原型：static int proxyCreateLockPath(const char \*lockPath)<br>
作用：创建锁文件和任何在lockPath中丢失的目录

70.
proxyCreateUnixFile()函数<br>
函数原型：static int proxyCreateUnixFile(<br>
    const char \*path,        //新unixFile的路径<br>
    unixFile \*\*ppFile,       //unixFile创建和返回通过ref<br>
    int islockfile           //如果非零丢失路径，则它将被创建<br>
)<br>
作用：创建一个新的VFS文件描述符(存书在通过sqlite3_malloc获得的内存)，并且在文件描述符中打开名为"path"。调用器不仅想关闭文件描述符负责，而且负责释放此文件描述符的关联内存：<br>
1.首先测试打开/创建次文件<br>
2.如果失败，并且它是一个锁文件(不是壳)，尝试创建父级目录然后重新尝试<br>
3.如果它失败了，尝试打开只读文件，在其他情况下返回BUSY(如果文件锁定)或者为了壳文件的CANTOPEN

71.
proxyGetHostID()函数<br>
函数原型：static int proxyGetHostID(unsigned char \*pHostID, int \*pError)<br>
作用：通过gethostuuid()得到宿主ID，pHostID必须指向可写内存的PROXY_HOSTIDLEN字节

72.
proxyBreakConchLock()函数<br>
函数原型：static int proxyBreakConchLock(unixFile \*pFile, uuid_t myHostID)<br>
作用：需要一个打开的壳文件,将内容复制到一个新的路径,然后移回。新创建的文件的文件描述符被分配给壳文件结构体并，最后原始壳文件描述符关闭。如果成功返回0

73.
proxyConchLock()函数<br>
函数原型：static int proxyConchLock(unixFile \*pFile, uuid\_t myHostID, int lockType)<br>
作用：给壳文件加上请求的锁，并打破旧的锁，如果主机ID匹配的话<br>
如果锁定失败（忙）：<br>
第一次尝试：获得壳文件当前的时间，等待0.5秒，然后重试。<br>
第二次尝试：失败，如果当前时间改变或主机标识是不同，等待10秒，然后重试。<br>
第三次尝试：打破锁，除非当前时间已经改变了。

74.
proxyTakeConch()函数<br>
函数原型：static int proxyTakeConch(unixFile \*pFile)<br>
作用：通过采用共享锁获得壳文件并读取其内容，如果lockPath非NULL，主机ID和锁定文件路径必须匹配。一个NULL lockPath意味着壳文件的lockPath将被使用，如果主机ID相匹配，或者一个新的锁路径将自动生成并写入壳文件。<br>
短暂读取或者版本格式不匹配意味着我们需要建立一个新的壳文件。如果主机标识匹配，并且这个壳文件中已经存在加锁路径，我们就试着使用这个路径，如果我们打不开这个路径，就使用自动生成的路径重试。对于自动命名的本地锁文件，只需检查主机ID，我们将使用已经在那里的本地锁定文件路径。如果壳不可写，不匹配，我们不能用它。如果我们创建了一个新的壳文件（而不仅仅是更新有效壳文件的内容），尝试匹配数据库的权限。我们无法通过旧的锁文件路径创建代理锁文件，就通过自动命名再试一次。如果我们提取的值来自壳文件或者路径被分配在堆栈上，需要复制路径。

75.
proxyReleaseConch()函数<br>
函数原型：static int proxyReleaseConch(unixFile \*pFile)<br>
作用：如果pFile在一个壳文件上持有锁，然后释放这个锁。

76.
proxyCreateConchPathname ()函数<br>
函数原型：static int proxyCreateConchPathname(char \*dbPath, char \*\*pConchPath)<br>
作用：给定一个数据库文件的名称,计算其壳文件的名称。将壳文件名存储在sqlite3\_malloc()获得的内存。使\* pConchPath指向新名字。成功返回SQLITE\_OK或者返回SQLITE_NOMEM，如果无法获得内存。调用者负责确认分配的内存空间是完全释放的*pConchPath设置为NULL，如果出现内存分配错误。

77.
proxyGetDbPathForUnixFile ()函数<br>
函数原型：static int proxyGetDbPathForUnixFile(unixFile \*pFile, char \*dbPath)<br>
作用：pFile是由之前xOpen调用打开的一个文件。dbPath是一个大小至少为MAXPATHLEN+1个字符的字符串缓冲区，这个程序是发现与pFile关联的文件名，并将其写入整型的dbPath。

78.
proxyTransformUnixFile ()函数<br>
函数原型：static int proxyTransformUnixFile(unixFile \*pFile, const char \*path)<br>
作用：需要一个已经填写unix文件并改变它，这样所有文件锁定将本地proxy锁文件上执行。以下字段保存在锁定环境,这样他们可以恢复并且unix结构在最短的时间内正确清理:<br>
 ->lockingContext<br>
 ->pMethod<br>

79.
proxyFileControl ()函数<br>
函数原型：static int proxyFileControl(sqlite3_file \*id, int op, void \*pArg)<br>
作用：这个程序处理特定的代理锁定的sqlite3\_file\_control()调用。

80.
proxyCheckReservedLock ()函数<br>
函数原型：static int proxyCheckReservedLock(sqlite3\_file \*id, int \*pResOut)<br>
作用：这个程序检查是否在这个或任何其他进程指定的文件持有一个未决锁。如果持有这样的锁，设置\*pResOut为一个非零值，否则设置\*pResOut为0.设置返回值为SQLITE\_OK，除非在锁检查期间发生一个I/O错误。

81.
proxyLock ()函数<br>
函数原型：static int proxyLock(sqlite3_file \*id, int eFileLock)<br>
说明：用参数eFileLock指定的锁锁定文件 - 以下之一：<br>
     (1) SHARED_LOCK             共享锁<br>
     (2) RESERVED_LOCK           保留锁<br>
     (3) PENDING_LOCK            未决锁<br>
     (4) EXCLUSIVE_LOCK          排它锁<br>
有时当请求一个锁状态，额外的锁状态会被插入在之间。在之后的一个转换锁定可能会失败，这个转换让锁状态与它开始不同但是仍然缺乏它的目标。下面的表显示了允许的转换和插入的中间状态。<br>
    UNLOCKED -> SHARED<br>
    SHARED -> RESERVED<br>
    SHARED -> (PENDING) -> EXCLUSIVE<br>
    RESERVED -> (PENDING) -> EXCLUSIVE<br>
    PENDING -> EXCLUSIVE<br>
这个程序将仅增加一个锁。使用sqlite3OsUnlock()程序来降低一个锁定级别。

82.
proxyUnlock ()函数<br>
函数原型：static int proxyUnlock(sqlite3\_file \*id, int eFileLock)<br>
作用：降低在文件描述符pFile到eFileLock上的锁定级别。eFileLock必须是无锁或者共享锁。如果文件描述符锁定的级别已经在或者低于请求锁定级别，这个例程是一个空操作。

83.
proxyClose ()函数<br>
函数原型：static int proxyClose(sqlite3_file \*id)<br>
作用：关闭一个文件，使用代理锁代理锁定风格目的是应用在AFP文件系统中，自从AFP只支持苹果MacOSX系统，代理锁定同样限定在MacOSX。

84.
sqlite3\_os\_init()函数<br>
函数原型：int sqlite3\_os\_init(void)<br>
作用：初始化操作系统接口，这个例程为所有类Unix操作系统的VFS实现注册。这个例程和紧随其后的sqlite3\_os\_end()例程应该是本文件中对其他文件可见的唯一例程这个例程在SQLite初始化时便会通过单线程调用。内存分配和互斥子系统不需要在这个例程被调用时初始化，所以它们不能被使用

85.
sqlite3\_os\_end()函数<br>
函数原型：int sqlite3\_os\_end(void)<br>
作用：关闭操作系统接口，一些操作系统可能需要自这个历程中做一些清理工作，去释放动态分配对象。但是不在Unix。这个例程对Unix不起作用。
