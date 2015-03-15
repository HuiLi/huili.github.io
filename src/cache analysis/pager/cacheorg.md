# 缓存组织
<img src="../p1.png"/>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sqlite中，数据是由B-tree组织的，如果B-tree需要从DB中获取某个页面，则通过pager，将页面从DB中加载到页面缓冲区：Page cache中。可简单认为Pcache：是分配在内存中的数据库缓存。</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(1)数据库（除了内存数据库）通常以常规的本地文件形式驻留在类似硬盘的外部存储设备上。当SQLite需要一个数据项，它将数据项从数据库文件中读入主存中，在内存中操作它。可用主存的是有限的，只有一部分的内存被用来存储小部分数据库文件，这部分存储内存空间通常被称为一个数据库缓存或者数据缓冲区，SQLite将每一个数据库（包括内存数据库）划分成固定大小的区域，它们被称为数据库页或简单页的。页面的大小是2的乘幂，大小能够在512到32768(都包含在内)之间；默认值是1024。</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(2)如果B-tree需要页面，它都会请求pager从disk读取数据，然后把页面(page)加载到页面缓冲区(page cache)，之后，B-tree和与之关联的游标就可以访问位于page中的记录了。</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;下面具体讲解一下cache具体结构。
