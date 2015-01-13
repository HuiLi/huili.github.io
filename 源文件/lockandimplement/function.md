# 各个锁机制实现函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;各个模式的锁都是由四个函数来实现的，首先是检查保留锁函数，然后是加锁函数，解锁函数，最后就是解锁之后的关闭文件函数。它们的步骤差不多都是一样，只是在具体实现的时候有一些细节的区别，会有不同的机制，调用其他不同的函数。<br>
1.3.1 CheckReservedLock（检查保留锁函数）
<img src="p3.png">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除了NFS锁，其他的锁模式都有检查保留锁函数，即CheckReservedLock，这个函数有两个参数，sqlite3\_file \*id,和int \*pResOut，id是文件ID，如果文件持有保留锁，将指针\*pResOout的值设为非零，否则设为0。但是无操作锁在检查的过程中会调用预定义的UNUSED\_PARAMETER函数，这个函数是专门为无操作锁定义的，只有无操作锁才会调用它，因为无操作锁不需要互斥，而这个函数的功能就是构建一个不需要互斥的数据库文件操作。我是这样理解的，因为无操作锁定它并不对数据库文件锁定后进行读或写，所以它是通过NotUsed指针参数查看是否有进程对指定文件进行操作，并且返回一个空值。然后把\*pResOout的值设为0，返回SQLITE\_OK。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在dot-file Locking机制中，锁要么存在要么不存在，所以函数CheckReservedLock()的变化就是如果文件被加锁，\*pResOut 的值设为true，文件没被加锁则设为false。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在AFP Locking中定义了属于它自己的一个结构体struct afpLockingContext，这个结构体包含所有苹果文件协议锁特定的状态。因为AFP Locking是利用字节来实现的，所以它在检查保留所之前会调用函数int afpSetLock在AFP文件系统中设置或清除比特范围内的锁。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;设置或清除成功就返回SQLITE\_OK，失败则返回SQLITE\_BUSY。做完这个操作之后才会检查是否有进程持有保留锁。下面是实现设置或清除比特范围内的锁的方法：<br>
static int afpSetLock(<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;const char \*path,          // 锁定或未锁定文件名称<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unixFile \*pFile,          // 打开文件描述符<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned long long offset,    // 锁定第一个字节<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;unsigned long long length,      // 锁定的字节数<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;int setLockFlag                // 成功就设置锁，失败则清除锁<br>
)<br>
1.3.2 Lock（加锁函数）
<img src="p4.png">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除了NFS锁，其他的锁模式都有加锁函数，即Lock。No-op Locking的nolockLock函数有sqlite3\_file \*NotUsed,和int NotUsed2两个参数，之所将nolockLock函数的两个参数命名为“NotUsed”和“NotUsed2”，是因为它本身并不使用这两个参数，而是通过将参数传递给预定义的函数UNUSED\_PARAMETER2来实现进程对数据库文件的访问的。而其他几个模式的加锁函数都有同样的两个参数sqlite3\_file \*id, int eFileLock，\*id是唯一的文件ID，eFileLock是当前文件所持有的锁类型。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;那么dot-file Locking，flock Locking，Named Semaphore Locking，AFP Locking都会使用参数eFileLock以下值之一指定的锁对文件进行加锁：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（1） SHARED\_LOCK：共享锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（2） RESERVED\_LOCK：保留锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（3） PENDING\_LOCK ：未决锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（4） EXCLUSIVE\_LOCK ：排他锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当然，请求一个锁状态的时候，额外的锁状态也会插入到其中。锁定失败后，后来锁的转换会使文件不同于开始时候的锁状态，但是仍能达到预期目标。以下的记录是允许的转换和插入的状态：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（1）UNLOCKED -> SHARED ：未加锁-->共享锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（2）SHARED -> RESERVED  ：共享锁转为-->保留锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（3）SHARED -> (PENDING) EXCLUSIVE ：共享锁-->未决锁（插入）-->排他锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（4）RESERVED -> (PENDING)  EXCLUSIVE ：保留锁-->未决锁（插入）-->排他锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（5）PENDING -> EXCLUSIVE ：未决锁-->排他锁<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（1）dot-file Locking<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在点文件锁中dotlockLock函数只是增加一个锁。虽然点文件锁实际上只支持第四种状态：排他锁，但是可以在内部追踪其他级别的锁。 如果指定文件有其中任意一种锁，那么锁文件其实已经存在了，我们要做的只是修改内部记录的锁级别，并且更新旧文件的时间戳。成功获取锁后，设置该锁的类型值并返回ok。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（2）flock Locking<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;聚集锁实际上也只支持排他锁。我们在sqlite3文件结构中追踪中间级别的锁状态，但是所有的共享锁或者以上级别的锁事实上都是排他锁，并且拒绝其它进程访问当前文件。
它也只是通过flockLock函数只是增加一个锁，当文件已经被锁定时，只需要修改锁的级别就可以。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（3）Named Semaphore Locking<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;命名信号锁和点文件锁、聚集锁一样，实际上也只支持排他锁。但是它是通过信号来进行文件加锁的，当我们需要一个锁的时候就将当前文件的锁信息的信号锁定，如果一个文件已经持有排他锁，就只是将其锁级别作调整，并且结束加锁请求，此时还要将之前锁定的信号释放。命名信号锁加锁函数如下：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;	static int semLock(sqlite3\_file \*id, int eFileLock)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（4）AFP Locking<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;由于AFP锁不同于前面介绍的几种锁，它不仅支持排他锁，其他三种类型的锁也是支持的，所以相对来说AFP锁的加锁过程就比较复杂。当我们要对一个文件进行加锁，而它已经有一个这种类型的锁或unixFile有更多的限制性，那就不做任何操作，也不要使用afp\_end\_lock:退出文件，因为unixEnterMutex函数尚未被调用，也就是说还没进入互斥状态。 并且要通过下面的代码捕获文件锁信息以确保锁定顺序是正确的：<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;assert( pFile->eFileLock!=NO\_LOCK || eFileLock==SHARED\_LOCK );<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;assert( eFileLock!=PENDING_LOCK );<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;assert( eFileLock!=RESERVED\_LOCK || pFile->eFileLock==SHARED\_LOCK );<br>
其顺序有如下三条：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1). 从未加锁状态只能转移到共享锁；<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2). SQLite不会明确请求一个未决锁；<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3). 当获取到保留锁时，共享锁也一直不能释放。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;由于因为pFile->pInode在线程之间是共享的，所以在获取锁之前调用unixEnterMutex函数进入互斥状态是必要的。如果有线程使用拥有一个不同的unixFile操作的进程ID去排除请求的锁，则返回BUSY。如果要请求一个共享锁，并且一些使用这个进程ID的线程已经持有一个共享锁或者保留锁，那么只需增加引用计数并且返回SQLITE\_OK。在获得共享锁和排他锁之前，需要获取保持一个临时的未决锁，获得共享锁之后，未决锁就会被释放掉。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在获取排他锁之前还必须将共享锁释放掉，如果还是不能获取AFP锁，则需要重建共享锁，如果出现I/O错误的话重建失败，如果成功就重新请求排他锁。排他锁获取成功就将文件的锁信息修改为当前锁级别，如果不成功则保持未决锁。加锁结束后需要调用unixLeaveMutex函数来释放互斥。<br>
1.3.3 Unlock（解锁函数）
<img src="p5.png">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前面提到的所有模式的锁都会调用Unlock函数进行解锁，大体流程差别不大，只是有一些细节的不同，还有就是无操作锁的解锁函数的参数跟其他的锁模式有点区别。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先是No-op Locking，它的nolockUnLock函数有两个参数，sqlite3\_file \*NotUsed,和int NotUsed2，就像加锁一样，nolockUnLock函数也是通过调用UNUSED\_PARAMETER2函数，并通过参数传递来对文件进行解锁的。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其次是其他五个模式的锁，它们的解锁函数都有同样的两个参数，分别是sqlite3_file \*id,和int eFileLock。虽然nfsUnlock的跟其他解锁函数的参数是一样的，但是它的实现是不同的，它是通过调用posixUnlock（可移植操作系统解锁）函数并返回其操作码对文件进行解锁。posixUnlock函数有一个参数是handleNFSUnlock，当这个参数为真的时候，就对通过字节范围对文件进行两部分解锁，其中一部分仅仅是解锁，而另外一部分要将它置为共享锁。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;而dot-file Locking、flock Locking、Named Semaphore Locking以及AFP Locking则是通过修改文件描述符pFile 的eFileLok来降低锁的级别 从而达到解锁的目的。修改后的eFileLock必须是NO\_LOCK or和SHARED_LOCK两者之一，如果文件描述符的锁定级别已经在正在请求的锁级别或以下，那么这个程序就不作任何操作。AFP Locking中，通过对共享锁计数器进行递减操作实现解锁。当且仅当同一进程中的所有线程都被释放掉共享锁，使用一个操作系统调用释放该锁。<br>
1.3.4 Close（关闭函数）
<img src="p6.png">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除了NFS Locking，其他模式的锁都会在解锁之后调用**Close函数，这个函数的功能是对文件的操作结束并解锁之后清除锁记录并关闭文件。其中，No-op Locking锁的 nolockClose函数实现是最简单的，它是调用closeUnixFile函数来关闭目录和文件句柄，将unixFile结构的所有字段设置为0并返回。 dotlockClose则是先调用自己的解锁函数将文件的锁级别设定为未加锁，再调用sqlite3_free函数释放之前获得的内存，然后再进行同样的操作。FlockClose在调用自己的解锁函数将文件的锁级别设定为未加锁后直接进行同样的操作。semClose又稍微复杂了一点，它在调用解锁函数后进入互斥状态，将文件的结点信息清空并且释放互斥，此时才能调用closeUnixFile函数关闭文件。afpClose在设置文件锁为未加锁之后需要进入互斥状态检查是否还有未完成的锁，如果还有未完成的锁，清理完这些锁之前不能关闭文件。相反,通过setPendingFd函数添加文件描述符pInode - >a Pending，它将在最后一个锁被清理掉之后自动关闭。然后释放结点信息和内存，最后调用 closeUnixFile函数关闭文件并释放互斥。
