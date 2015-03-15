# 通过unfree list检查内存泄露
	struct MemBlockHdr {
	  i64 iSize;                          /* Size of this allocation */
	  struct MemBlockHdr *pNext, *pPrev;  /* Linked list of all unfreed memory */
	  char nBacktrace;                    /* Number of backtraces on this alloc */
	  char nBacktraceSlots;               /* Available backtrace slots */
	  u8 nTitle;                          /* Bytes of title; includes '\0' */
	  u8 eType;                           /* Allocation type code */
	  int iForeGuard;                     /* Guard word for sanity */
	};
Unfree list 检查内存泄露主要是通过结构体MemBlockHdr的指针*pNext,和*pPrev来判断内存是否发生了泄露问题。
