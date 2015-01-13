# mem2.c
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mem2主要用于调试，增加了用于调试的附属结构。使用SQLITE_MEMDEBUG启用。

Each memory allocation looks like this:
**  ------------------------------------------------------------------------
**  | Title |  backtrace pointers |  MemBlockHdr |  allocation |  EndGuard |
**  ------------------------------------------------------------------------
**
Title:用于描述这段内存，在出错时可以打印出来

backtrace pointer：用于保留调用堆栈

MemBlockHdr:负责这片内存的管理，以及串联unfree的MemBlock

allocation：分配给上层的空间

EndGuard：尾部的哨兵，用于检查内存被踩。还有个“HeadGaurd ”在MemBlockHdr中。


关于backtrace：

1，可以设置malloc的backtrace的深度

2，在生成生成backtrace后可以通过回调函数将其传出去

/*
** Open the file indicated and write a log of all unfreed

memory allocations into that log.（这里会打印调用栈）
*/

void sqlite3MemdebugDump(const char *zFilename)

2，设置哨兵对内存破坏进行检查

/*
** Given an allocation, find the MemBlockHdr for that allocation.

**

** This routine checks the guards at either end of the

allocation and if they are incorrect it asserts.
*/

static struct MemBlockHdr *sqlite3MemsysGetHeader(void

*pAllocation)

3，通过unfree list检查内存泄露

struct MemBlockHdr {

  i64 iSize;                          /* Size of this allocation */

  struct MemBlockHdr *pNext, *pPrev;  /* Linked list of all unfreed memory */
