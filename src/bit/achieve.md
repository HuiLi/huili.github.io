# 具体实现及源码分析
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bitvec.c文件实现了一个对象，用这个对象表示一个固定长度的位图，位从1开始编号。当对象被创建时，位图的大小就被固定了。当位图被创建时所有的位都被清空，单独的位可能会被设置或清除一次。
在位图中，测试操作比集合操作要通用100倍，也就是说测试操作更加常用，清除操作是极其少见的。尽管集合的数量有时会增长到成千上万或者更多，但是每个Bitvec对象通常有5到500个集合操作。Bitvec对象的大小是在事务开始时数据库文件中的页的数量，因此通常不到几千比特，但是像20亿那么大的数据库，位图也可以大到20亿比特。
1）下面的结构体是一个位图的实例

    struct Bitvec {
    u32 iSize;   /*最大位指数，最大值为4,294,967,296。*/
    u32 nSet;       /*被设置的位的数量，仅适用于aHash元素，最大是BITVEC_NINT。因为BITVEC_SZ 的大小是512，所以这里是125。*/
    u32 iDivisor;        /*位的数量由每个apSub[]的记录处理。最大的iDivisor是max(u32)/BITVEC_NPTR+1。因为BITVEC_SZ的大小是512，所以这里是34,359,739。*/
      union {
    BITVEC_TELEM aBitmap[BITVEC_NELEM];   /* 位图的表示 */
    u32 aHash[BITVEC_NINT];        /* hash表的表示 */
        Bitvec *apSub[BITVEC_NPTR];  /* 递归的表示 */	       } u;
    };
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个位图记录0或更多位的存在，用在1和iSize之间的值记录，并且包含1和iSize。如果iSize<=BITVEC_NBIT，那么Bitvec.u.aBitmap[]是一个直接的位图，它的最低有效单位是1位。
如果iSize>BITVEC\_NBIT并且iDivisor==0，那么Bitvec.u.aHash[]是一个能够容纳BITVEC\_MXHASH的不同值的hash表。（hash表主要是加快对缓存中数据页的查找速度。hash表后面是一串链表，保存满足该hash函数的所有的页。SQLite是通过页号来进行hash操作的，hash完找到链表的头结点，然后依次查找。）否则，值i会被重新传递给由Bitvec.u.apSub[]所指的BITVEC_NPTR子位图。每一个子位图句柄多不同于值i。apSub[0]的值介于1和iDivisor之间。apSub[1]的值介于iDivisor+1和2\*iDivisor之间。apSub[N]的值介于N\*iDivisor+1和(N+1)*iDivisor之间，而每一个子位图都能很规范的处理介于1到iDivisor之间的值。

2）

    Bitvec *sqlite3BitvecCreate(u32 iSize){
      Bitvec *p;
      assert( sizeof(*p)==BITVEC_SZ ); /*定义新位图对象的大小为预定义过的BITVEC_SZ大小，即512字节 */
      p = sqlite3MallocZero( sizeof(*p) ); /* 调用API函数分配内存空间 */
      if( p ){
        p->iSize = iSize;
      }
      return p;
    }
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个函数用来创建一个能过处理0到iSize个位（包含0和iSize）的新位图对象，返回新对象的指针。如果分配失败返回空。

3）

     int sqlite3BitvecTest(Bitvec *p, u32 i){}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;检查第i个位是否被分配，返回真或假。如果p为空(如果位图没有被创建)或i溢出则返回假。

4）

    int sqlite3BitvecSet(Bitvec *p, u32 i){}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;分配第i个位，成功时返回0，如果出错则返回错误信息。需要注意的是，这个程序可能会分配子位图。假设p和i是有效的，那么对于插入来说需要存放子位图却没有获得内存是唯一可能出错的地方。
调用函数必须确保p是有效的Bitvec对象，并且i的值没有溢出Bitvec对象的范围，否则行为是无效的。

5）

    void sqlite3BitvecClear(Bitvec *p, u32 i, void *pBuf){}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;清除第i位，这里的pBuf必须是一个至少为BITVEC_SZ个字节的指针指向临时存储器，通过临时存储器BitvecClear可以重建hash表。

6）

    void sqlite3BitvecDestroy(Bitvec *p){}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;销毁一个位图对象，回收所有可用内存。


7）

    u32 sqlite3BitvecSize(Bitvec *p){}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当Bitvec *p被创建时返回参数iSize指定的值。令V[]是一个无符号字符数组并有足够的空间容纳N位。令I是一个介于0和N之间的整数（0<=I<N），然后剩下的模块就可以在V中用来分配，清除或测试单独的位。

    #define SETBIT(V,I)      V[I>>3] |= (1<<(I&7))
    #define CLEARBIT(V,I)    V[I>>3] &= ~(1<<(I&7))
    #define TESTBIT(V,I)     (V[I>>3]&(1<<(I&7)))!=0

8）

    int sqlite3BitvecBuiltinTest(int sz, int *aOp){}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个函数可以广泛的测试Bitvec代码。输入是一个可以作为程序检测Bitvec的整型数组。操作码是整数，有从0到5一共6个操作码。0是“停止”操作码，表示测试结束。

    0          停止并返回错误个数
    1 N S X    分配N位从S开始递增的X
    2 N S X    清除N位从S开始递增到X
    3 N        分配N个随机的位
    4 N        清除N个随机的位
    5 N S X    只在数组中分配N位从S递增到X，不在bitvec中
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;操作码1到4执行分配，清除操作在Bitvec对象和从malloc中得到的位线性数组上都执行。操作码5只在线性数组上起作用，在Bitvec上不行。操作码5特意用来引导错误，为了确认错误，起检测作用。最后检测线性数组的结论是和Bitvec对象作比较。如果有不同，就返回错误。如果相同，返就回0。如果内存分配出现错误，则返回-1。






