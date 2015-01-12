# File header

1.文件以1号页面开始，它包括100个字节的文件头。

2.Header string(头字符串)：16个字节，"SQLite format 3"

3.Page size（页面大小）：1024

4.File format（文件格式）：在当前的版本都为1。

5.Reserved space（保留空间）：1个字节，SQLite在每个页
面的末尾都会保留一定的空间，留作它用，默认为0。


6.Embedded payload：max embedded payload fraction(偏移21)的值限定了B树内节点（页面）中一个元组（记录，单元）最多能够使用的空间。255意味着100%，默认值为0x40，即64（25%），这保证了一个结点（页面）至少有4个单元。如果一个单元的负载(payload，即数据量)超过最大值，则溢出的数据保存到溢出的页面，一旦SQLite分配了一个溢出页面，它会尽可能多的移动数据到溢出页面，下限为minembedded payload fractionvalue（偏移为22），默认的值为32，即12.5% 。

7.File change counter（文件修改计数）：通常被事务使用，它由事务增加其值。该值的主要目的是数据库改变时，pager避免对缓存进行刷盘。

8.Freelist（空闲页面链表）：在文件头偏移32的4个字节记录着空闲页面链的第一个页面，偏移36处的4个字节为空闲页面的数量。
<img src = "5.png">

9.文件头的指针指向空闲链表的第一个trunkpage，每个trunk page指向多个叶子页面

10.Trunk page的格式如下，从页面的起始处开始：

（1）4个字节，指向下一个trunk page的页面号；

（2）4个字节，该页面的叶子页面指针的数量；

（3）指向叶子页面的页面号，每项4个字节。

11.Meta variables（元数据变量）：
从偏移为40开始，为15个4字节的元数据变量，这些元数据主要与B树和VM有关。


