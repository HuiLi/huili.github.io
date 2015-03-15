# 结构体定义
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;1.结构体vdbesorter的定义<br>
（1）struct VdbeSorter {<br>
  		i64 iWriteOff; /* 文件ptemp1（存储PMA的文件）中，当前的写偏移量*/<br>
 	    i64 iReadOff; /*文件ptemp1（PMA file 1）中，当前的读偏移量*/<br>
        int nInMemory; /*作为PMA的pRecord list的当前大小*/<br>
  	    int nTree; /* aTree/aIter的已用大小（2的幂）*/<br>
  	    int nPMA; /*文件pTemp1中存储的PMA的数量*/<br>
        int mnPmaSize; /* PMA至少多大（按字节数算）*/<br>
  		int mxPmaSize; /* PMA至少多大（按字节数算）*/<br>
  		VdbeSorterIter *aIter; /*存储要合并到一起的iterator的VdbeSorterIter的数组*/<br>
        int *aTree; /* 增量合并的当前状态*/<br>
        sqlite3_file *pTemp1; /*指向存储PMA的文件的指针*/<br>
        SorterRecord *pRecord; /*内存中记录列表的头*/<br>
        UnpackedRecord *pUnpacked; /*用来解包keys*/
    };<br>
	（2）下面这个结构体是这是本源文件开头处声明的第1个结构体的定义，定义了PMA的iterator，把当前的key储存在变量nKey/aKey中，若iterator在EOF处，sqlite3_file类型的指针pFile==0。<br>
	struct VdbeSorterIter {<br>
  		i64 iReadOff; /*当前读偏移量*/<br>
  		i64 iEof; /*此变量位于EoF后面，距离EoF一个字节*/<br>
  		int nAlloc;/* aAlloc处空间的字节数*/<br>
 		 int nKey; /* key占用的字节数*/<br>
  		sqlite3_file *pFile; /*此指针所指的地方是ｉｔｅｒａｔｏｒ开始读的地方*/<br>
  		u8 *aAlloc; /*已经分配出去的空间*/<br>
  		u8 *aKey; /*指向当前ｋｅｙ的指针*/<br>
  		u8 *aBuffer; /*当前的读缓存*/<br>
  		int nBuffer; /*读缓存的字节数*/<br>
};<br>
	(3)下面这个结构体的实例用来组织记录流，是本源文件开头处声明的第3个结构体的定义这些记录流将按照mergecod的算法写入到文件中的对齐的、页面大小的块中。
	struct FileWriter {<br>
		int eFWErr; /*当处于错误状态时是个非零值*/<br>
		u8 *aBuffer;/*指向写缓存的指针*/<br>
		int nBuffer; /*写缓存的字节数*/<br>
		int iBufStart; /*缓存中要写入的第一个字节*/<br>
		int iBufEnd; /*缓存中要写入的最后一个缓存*/<br>
		i64 iWriteOff;/*缓存开始处在文件中的偏移量*/<br>
		sqlite3_file *pFile; /*此指针指向将被写入数据的文件的指针*/<br>
	};<br>
(4)下面这个结构体用来存储一个单独的记录。所有内存中的记录被连接成一个链表，链表的头SorterRecord *pRecord由指针SorterRecord *pNext指向。
	struct SorterRecord<br> {//这是本源文件开头处声明的第2个结构体的定义<br>
  		void *pVal;<br>
 		 int nVal;<br>
  		SorterRecord *pNext;<br>
	};<br>
