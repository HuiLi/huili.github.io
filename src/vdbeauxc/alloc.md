# 分配空间
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;该功能由函数static void *allocSpace( void *pBuf，int nByte，u8 **ppFrom，u8 *pEnd，
 int *pnByte ){}实现。
程序运行中需要从一个固定大小的缓冲区分配空间并返回一个指向该空间的指针。如果可用空间不足，则返回空。各参数的功能如下：
pBuf是一个指针的初始值，用来接收新的内存，一般都为空。如果pbuf不为空，意味着存储空间已经被指派了，并且这个程序不应该分配新的内存。当pbuf不为空时直接返回pbuf，只有pbuf为空的时候才分配存储空间。
nByte是所需的空间的字节数。
*ppFrom指向可用的空间，pEnd指向可用空间的末尾。当空间被分配好后，*ppFrom
会越过该分配空间的末尾。
*pnByte是分配失败的空间字节数的计数器。如果*ppFrom没有足够的空间来满足请求，
那么增加*pnByte请求的数量。
