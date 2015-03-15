# mem5.c

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mem5相对mem3主要是算法上的改变。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要作用是防止内存分配失败。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于动态内存分配问题、内存分配失败问题，J.M.Robson进行过系统的研究，其结果发表在如下论文中：
J. M. Robson. "Bounds for Some Functions Concerning Dynamic Storage Allocation". Journal of the Association for Computing Machinery, Volume 21, Number 8, July 1974, pages 491-499.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个版本的内存分配子系统省去了所有使用malloc（）该应用程序提供了SQLite的内存块的分配sqlite3_initialize()之前调用和返回的xMalloc()和xRealloc()实现。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一旦sqlite3_initialize()被称为可用的内存。仅当SQLITE_ENABLE_MEMSYS5被定义时，

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个版本的内存分配子系统将包括在建。
我们使用下面的记号（与Robson的记号类似，但不完全等同）：

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;N: 内存分配系统为了保证不会出现分配失败而需要的原始内存数量。

   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;M: 应用程序曾经在任何时间点取出的最大内存数量。

   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;n: 最大内存分配与最小分配的比值。我们假设每个内存分配大小都是最小分配大小的整数倍。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Robson证明了下面的结果：

    N = M*(1 + (log2 n)/2) - n + 1
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;通俗地讲，Robson证明表明，为了防止内存分配失败，任何内存分配器必须使用一个大小为N的内存池，它超过曾经使用的最大内存M乘以一个取决于n的倍数。也就是说，除非所有的内存分配都是同样的大小，否则系统需要访问比曾经使用的更大的内存。此外我们看到，需要的剩余内存随着比值n的增加会迅速的增长，因此我们应该保持所有的内存分配尽可能大小相同。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Robson证明是构造性的。他提出一个算法来计算一个分配和释放操作系列由于内存碎片（可用内存大于1字节但小N字节）将导致分配失败。Robson还证明如果可用内存为N或更多的字节，一个“2的幂，首次命中”的内存分配器绝不会出现内存分配失败。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;M和n值是应用程序的属性。如果创建应用程序时M和n值是已知的，或者至少知道上限值；并且如果应用程序使用memsys5内存分配器，通过SQLITE_CONFIG_HEAP提供N字节的可用内存空间，Robson证明在应用程序中不会出现内存分配请求失败。也就是说，应用程序开发者可以选择一个N值，以保证对SQLite任何接口的调用不会返回SQLITE_NOMEM，内存池也不会出现碎片以致于不能满足新的内存分配请求。这在哪些一个软件故障就会导致损坏或关键数据丢失的应用程序中至关重要。

Mem5:

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;计算和控制参数M和n

Robson证明可以用在SQLite的以下内存分配器中：
* 通用内存分配器（memsys5）
* 临时内存分配器
* 页缓存内存分配器
* 后备内存分配器
对除memsys5之外的其他内存分配器，所有的内存分配都是同样大小的，因此n=1，N=M。也就是说，内存池无需比任何时刻使用的内存最大量还要大。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite保证没有线程同时使用超过两个的临时内存槽，因此，如果应用程序分配线程数量两倍的临时内存槽，并且每个槽足够大，临时内存分配器不会溢出。临时内存分配大小的上限是最大页面的6倍，因此很容易保证临时内存分配器不出现分配失败的操作。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在SQLite 3.6.1中，页缓存内存的使用比较难控制，唯一控制页缓存内存的方式是使用cache_size pragma指令。在以后的SQLite版本中会使页缓存内存控制变得更容易。
sqlite3_db_config(db, SQLITE_DBCONFIG_LOOKASIDE, aStatic, 256, 500);

   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;后备内存分配器主要是用于性能优化，而不是防止内存分配失败。因此对安全悠关的应用程序完全禁用后备内存分配器是合理的。

 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  通用内存分配器是最难管理内存池的，因为它支持不同大小的分配。因为n是在M上的倍数，我们需要让n尽可能地小，并保持memsys5的最小分配大小尽可能地大。在很多应用程序中，后备内存分配器能够处理小块内存的分配。因此设置memsys5的最小分配大小为后备内存最大分配的2，4或8倍是合理的，512字节的最小分配大小设置比较合理。
此外为了让n很小，我们可能希望最大内存分配大小在可控范围之内。通用内存分配器上的大分配请求可能有这么几个来源：

（1）SQL表中包含长字符串或BLOBs的行。

（2）编译成大的预处理语句对象的复杂SQL查询。

（3）sqlite3_prepare_v2()内部使用的SQL解析对象。

（4）数据库连接的存储空间。

（5）外流到通用内存分配器的临时内存分配。

（6）外流到通用内存分配器的页缓存内存分配。

（7）新数据库连接的后备内存分配。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;数据库连接对象需要的存储空间取决于数据库文件名的长度范围，但是在32位系统上很少超过2KB（在64位系统上由于指针大小的增加而需要更多空间）。每个解析对象大概使用1.6KB的内存。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果数据库包含长的字符串或BLOBs，则应该用增量式BLOB I/O来读取它们，其更新也应该使用增量式BLOB I/O方法，而不用其他的方法。否则，sqlite3_step()将不得不在某一时刻读取整个行到连续的内存中，这涉及到至少一次大的内存分配。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;内存分配的最后一个来源是复杂SQL查询编译成的预处理语句对象。SQLite开发者正在进行的工作是减少这部分的内存空间需求。但是大的复杂SQL查询仍然需要几KB的预处理语句对象。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;目前的变通方案是把复杂SQL操作分离成多个小的简单的操作，以包含在各个单独的预处理语句对象中。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 考虑所有的情况后，应用程序通常可以让最大内存分配保持在2K或4K。这使log2(n)的值为2或3，限制N在M的2到2.5倍。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;应用程序需要的一次通用内存分配最大值取决于同时有多少个打开的数据库连接、预处理语句对象，以及预处理语句对象的复杂性。对任何给定的应用，这些因素通常是固定的，并且可以实验性地使用SQLITE_STATUS_MEMORY_USE来确定。一个典型的应用程序可能只使用40KB的通用内存，这使得N的值大约为100KB。

延性破坏

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果SQLite的内存分配子系统被配置成不会有内存分配失败，但实际内存使用超过Robson证明设置的限制，SQLite将继续正常地操作。临时内存分配器、页缓存内存分配器和后备内存分配器自动切换到memsys5通用内存分配器。通常memsys5内存分配器将继续执行分配功能，即使M或n超过设置的限制也不会有内存碎片。Robson证明表明在这种情况下一个内存分配有可能失败，但是这样的失败需要一种特殊的分配和释放顺序，在SQLite中没有这样的分配释放顺序。因此在实践中，Robson限制可以超过而不会出现坏的影响。
