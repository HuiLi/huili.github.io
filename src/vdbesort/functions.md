# 函数定义
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;由于本文件中定义的函数实在太多，而本报告篇幅有限，所以下面简要介绍一下本原文件中所定义的函数，只列出了函数中函数的声明部分。
    （1）static void vdbeSorterIterZero(sqlite3 *db， VdbeSorterIter *pIter){}
	该方法的功能就是释放由第二个参数VdbeSorterIter *pIter指向的VdbeSorterIter实例的内存空间。 该函数会被第十个定义的函数调用。
	（2）static int vdbeSorterIterRead(sqlite3 *db， VdbeSorterIter *p， int nByte， u8 **ppOut ){}
	从由迭代器对象p迭代的数据流中读出nByte字节的数据。如果读取成功的话，令指针ppout指向包含读出的数据的缓存并返回SQLITE_OK。否则，如果出现错误，返回一个SQLite error代号。由ppOut指向的缓存只在下次调用该函数之前是有效地。
	如果缓存中没有数据可读了，就从文件中读出接下来的大小等于nBuffer字节的数据存入缓存中，如果PMA中的字节数小于nBuffer，就把剩下的所有数据读出来。
	（3）static int vdbeSorterIterVarint(sqlite3 *db， VdbeSorterIter *p， u64 *pnOut){}
	下面这个函数的功能是：从和参变量VdbeSorterIter *p相对应的数据流中读一个varint（可变长整数）， 并使指针pnOut指向读出来的这个数。
	（4）static int vdbeSorterIterNext(sqlite3 *db，VdbeSorterIter *pIter){}
	使迭代器pIter前进到对应PMA的下一个key，如果没有错误发生就返回SQLITE_OK，如果有错误发生，就返回SQLite error。
	（5）static int vdbeSorterIterInit(sqlite3 *db， const VdbeSorter *pSorter， i64 iStart， VdbeSorterIter 	*pIter， i64 *pnByte ){}
	初始化一个用来扫描文件pFile中PMA的迭代器pIter，扫描的开始和结束位置是：开始于偏移量位iStart的位置，结束于偏移量为iEof-1的位置。这个函数最后会使迭代器指向对应PMA的第一个位置（或EOF位置，如果ＰＭＡ＼是空的话）。
	（6）static void vdbeSorterCompare(){}
	这个函数用来比较key1和key2。参数pKeyInfo提供比较时要使用的校对功能。如果有错误发生就返回一个SQLite错误码，否则就返回SQLITE_OK，并给*pRes赋值，如果key1小，就赋负值，如果二者相等就赋０，若key1大，就赋正值。如果函数的参数bOmitRowid是非零的，就假设两个keys在rowid域结尾。基于比较的目的，忽略这种情况。如果bOmitRowid是真值，key1仅含有一个单独的NULL值，那么key1小于key2，甚至在key2也包含一个NULL值得情况下。如果pKey2由一个空指针代表，则假设pCsr->aSpace已经被分配并包含一个未包装的记录，被当做key2使用。
	（7）static int vdbeSorterDoCompare(const VdbeCursor *pCsr， int iOut){}
	这个函数在合并多元ｂ树段 时被调用。参数iOut是aTree[]中要被重新计算值的元素的下标值（结合开头时讲的N路归并算法，数组aTree[]是需要更新的）。
	（8）int sqlite3VdbeSorterInit(sqlite3 *db， VdbeCursor *pCsr){}
	此函数初始化临时索引游标，使之作为sorter游标。
	（9）static void vdbeSorterRecordFree(sqlite3 *db， SorterRecord *pRecord){}
	函数的功能：从pRecord所指的地方开始释放已排好序的记录列表。
	（10）void sqlite3VdbeSorterClose(sqlite3 *db， VdbeCursor *pCsr){}
	释放任何一个由sqlite3VdbeSorterXXX routine部署的游标元素。
	（11）static int vdbeSorterOpenTempFile(sqlite3 *db， sqlite3_file **ppFile){}
	此函数用来为文件句柄分配空间，并开辟一个临时文件。如果成功了，设置指针ppFile 指向 malloc的文件句柄并返回SQLITE_OK，否则将*ppFile设为0并返回错误代码。
	（12）static void vdbeSorterMerge(const VdbeCursor *pCsr， SorterRecord *p1， SorterRecord *p2，SorterRecord **ppOut ){}
	这个函数把两个已经排好序的列表合并成一个，并把ppOut指向新列表的表头。
	（13）static int vdbeSorterSort(const VdbeCursor *pCsr){}
	此函数对头在pCsr->pRecord处的记录链表排序。成功就返回SQLITE_OK；否则，返回SQLite错误码。
	（14）static void fileWriterInit(sqlite3 *db， qlite3_file *pFile， FileWriter *p， i64 iStart){}
	此函数用来初始化一个file-writer的实例。
	（15）static void fileWriterWrite(FileWriter *p， u8 *pData， int nData){}
	下面的函数往file-write实例中写大小为nData字节的数据，成功就返回SQLITE_OK
	否则返回SQLite错误码。
	（16）static int fileWriterFinish(sqlite3 *db， FileWriter *p， i64 *piEof){}
	把所有缓存的数据都存到磁盘上，并清空file-writer实例，调用完下面的函数后再使用file-writer所得到的结果是未知的，成功就返回SQLITE_OK，失败就返回错误码，在return之前，把最后写入的一个字节的后面的字节所对应的偏移量赋给*piEof。
	（17）static void fileWriterWriteVarint(FileWriter *p， u64 iVal){}
	这个函数把形参iVal（编码成一个可变长整数变量）的值传到一个file-write实例中
	成功返回SQLITE_OK，失败则返回错误码。
	（18）static int vdbeSorterListToPMA(sqlite3 *db， const VdbeCursor *pCsr){}
	函数的功能是把内存中的链表的当前内容写到一个PMA中。
    成功就返回SQLITE_OK，否则就返回一个错误码。
	PMA的格式如下：个可变长的整数变量，这个变量中存储有PMA中所有内容的字节大小（不包含改变量自己），一个或多个记录以尾对尾的方式、按照key的递增顺序排序。每条记录都由一个可变长整数和其后的一系列数据组成。可变长变量的值等于其后一系列的数据占用的字节的数目。
	（19）int sqlite3VdbeSorterWrite(sqlite3 *db， const VdbeCursor *pCsr， Mem *pVal){}
	功能是把一个记录添加到sorter。
	（20）static int vdbeSorterInitMerge(sqlite3 *db，const VdbeCursor *pCsr，i64 *pnByte){}
	此函数是函数sqlite3VdbeSorterRewind()的辅助函数。
	（21）int sqlite3VdbeSorterRewind(sqlite3 *db， const VdbeCursor *pCsr， int *pbEof){}
	一旦一个sorter被增添，这个函数就被调用以排好的顺序遍历sorter的内容。
	（22）int sqlite3VdbeSorterNext(sqlite3 *db， const VdbeCursor *pCsr， int *pbEof){}
	前进到sorter中的下一个元素。
	（23）static void *vdbeSorterRowkey(const VdbeSorter *pSorter， int *pnKey ){ }
	返回一个指针给buffer，这个buffer是包含当前key的sorter的。
	（24）int sqlite3VdbeSorterRowkey(const VdbeCursor *pCsr， Mem *pOut){ }
	此函数功能是把当前sorter key复制到内存单元pOut中。
	（25）int sqlite3VdbeSorterCompare(const VdbeCursor *pCsr， Mem *pVal， int *pRes){}
	把内存单元pVal中的key和由sorter cursor当做第一个参数传出来的key相比较；忽略每条记录末尾处的rowid域；如有错误发生，就返回一个SQLite错误码；否则把*pRes设置成一个负数，0，或正数，分别对应pVal中的key比当前sorter key小、相等或大。
