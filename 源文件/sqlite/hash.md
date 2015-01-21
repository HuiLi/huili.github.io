# Harsh
（1）哈希表的定义<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;散列表（Hash table，也叫哈希表），是根据关键码值(Key value)而直接进行访问的数据结构。即通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。映射函数叫做散列函数，存放记录的数组叫做散列表。<br></br>
（2）哈希表的寻址方法
1. 直接寻址法：取关键字或关键字的某个线性函数值为散列地址。即H(key)=key或H(key) = a•key + b，其中a和b为常数（这种散列函数叫做自身函数）。若其中H(key）中已经有值了，就往下一个找，直到H(key）中没有值了，就放进去。<br></br>
2. 数字分析法：分析一组数据，比如一组员工的出生年月日，这时我们发现出生年月日的前几位数字大体相同，这样的话，出现冲突的几率就会很大，但是我们发现年月日的后几位表示月份和具体日期的数字差别很大，如果用后面的数字来构成散列地址，则冲突的几率会明显降低。因此数字分析法就是找出数字的规律，尽可能利用这些数据来构造冲突几率较低的散列地址。<br></br>
3. 平方取中法：取关键字平方后的中间几位作为散列地址。<br></br>
4. 折叠法：将关键字分割成位数相同的几部分，最后一部分位数可以不同，然后取这几部分的叠加和（去除进位）作为散列地址。数位叠加可以有移位叠加和间界叠加两种方法。移位叠加是将分割后的每一部分的最低位对齐，然后相加；间界叠加是从一端向另一端沿分割界来回折叠，然后对齐相加。<br></br>
5. 随机数法：选择一随机函数，取关键字的随机值作为散列地址，通常用于关键字长度不同的场合。<br></br>
6. 除留余数法：取关键字被某个不大于散列表表长m的数p除后所得的余数为散列地址。即 H(key) = key MOD p,p<=m。不仅可以对关键字直接取模，也可在折叠、平方取中等运算之后取模。对p的选择很重要，一般取素数或m，若p选的不好，容易产生同义词。<br></br>

<br></br>（3）哈希表处理冲突的方法
1. 开放寻址法：Hi=(H(key) + di) MOD m,i=1,2，…，k(k<=m-1），其中H(key）为散列函数，m为散列表长，di为增量序列，可有下列三种取法：<br></br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.1. di=1,2,3，…，m-1，称线性探测再散列；<br></br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.2. di=1^2,-1^2,2^2,-2^2，⑶^2，…，±（k)^2,(k<=m/2）称二次探测再散列；<br></br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.3. di=伪随机数序列，称伪随机探测再散列。<br></br>
2. 再散列法：Hi=RHi(key),i=1,2，…，k RHi均是不同的散列函数，即在同义词产生地址冲突时计算另一个散列函数地址，直到冲突不再发生，这种方法不易产生“聚集”，但增加了计算时间。<br></br>
3. 链地址法（拉链法）。<br></br>
4. 建立一个公共溢出区。<br></br>


（4）哈希表的经典实现
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在对sqlite3_pcache接口的缺省实现PCache1结构中使用了哈希表来组织和管理缓冲区中的页面，主要在pcache1.c中出现，基本的实现方式就是同理论方式一样，使用一个数组组织哈希表，冲突消解策略使用的是链表；

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;哈希表apHash是一个PgHdr1类型指针的数组，使用页面编号作为键值，哈希函数是键值除以数组的长度；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果需要增加哈希表的尺寸（数组的长度），那么需要将表中的元素重新插入，不同于在berkely DB中只用重新插入对应的桶中的部分项，但是由于是在内存中使用，不用考虑磁盘的读写，这样处理简单可行；

（5）伸缩型哈希表<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;所谓的伸缩性的哈希表其实就是在数据量较小的时候使用双向链表存储，当数据量大的时候该用哈希表存储；这在一些类库中具有对应的数据结构供直接使用，sqlite3中使用了其中的一种实现；
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;哈希表的使用在sqlite3中，作为一个单独的模块来实现，hash.h和hash.c文件用于实现这一哈希表；
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;哈希表结构为Hash：使用四个成员变量存储：<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;htsize：无符号短整型，当使用哈希表存储时，哈希表中桶的数量；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;count：无符号短整型，哈希表中入口项的个数（记录的个数）；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;first：HashElem类型，哈希元素指针，指向入口项双向链表的表头；<br></br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ht：_ht结构指针，哈希表存储结构，当使用哈希表存储时，将表现为一个桶的数组；含有两个成员变量，其中count成员变量记录本桶中记录的数量，chain成员变量指向桶内记录链表的表头，哈希表使用的冲突消解策略是链表；
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;关于Hash的使用，当存储的记录数量较少时，将htsize赋值为0，ht赋值为空，所有记录实际保存在以first为表头的双向链表中，count是记录的数量；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当记录数量达到一定的程度后，使用ht配合哈希访问，htsize记录了ht桶的数量，即ht数组的长度。需要注意的是此时first链表仍然保存了哈希表中所有的记录，ht数组仅仅相当于索引的作用，即ht中每个桶都有自己的链表，这些链表链接在一起组成了first链表，ht中chain成员指向本桶链表的表头在first中的位置，count成员则记录了链表的长度，即确定表尾的位置；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从而，上述方式可以有效的达到哈希表伸缩转换的目的。
（6）哈希元素结构HashElem：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 具有5个成员变量：     <br></br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;next：下一条记录；    <br></br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;prev：上一条记录；    <br></br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;data：数据；     <br></br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;pKey：键；     <br></br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nKey：键的长度；     <br></br>


    sqlite3HashInit：哈希表的初始化函数，将各个成员变量初始化为0；
    void sqlite3HashInit(Hash *pNew){/*hash_table initliaze *///Elaine debug
      assert( pNew!=0 );/*如果括号中的值为0，就跳出；如果为1，继续执行*/
      pNew->first = 0;/*initialize the pointer of pNew *///Elaine debug
      pNew->count = 0;
      pNew->htsize = 0;
      pNew->ht = 0;
    }


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3HashClear：将哈希表中所有的入口项删除，就是将ht的内存释放，将first链表中所有的元素释放；将各个成员变量置0；



         void sqlite3HashClear(Hash *pH){
      HashElem *elem;   /* For looping over all elements of the table *//*循环表中的所有元素*/
      assert( pH!=0 );/*判读PH是否为0,如果为0,则说明HASH为空，程序会中止，否则说明HASH不为空继续执行*/
      elem = pH->first;
      pH->first = 0;
      sqlite3_free(pH->ht);/*释放HASH函数的ht*/
      pH->ht = 0;//使哈希表的ht为0
      pH->htsize = 0;/*htsize is unsigned int,*///Elaine debug
      while( elem ){//判断elem是否还存在，如果存在继续做释放的动作
        HashElem *next_elem = elem->next;
        sqlite3_free(elem);//释放elem
        elem = next_elem;//elam等于他的下一个elem，依次后移
      }
      pH->count = 0;//表中的count置为0
    }


 InsertElement函数：向哈希表中插入元素；

     static void insertElement(
      Hash *pH,              /* The complete hash table *///完整的哈希表
      struct _ht *pEntry,    /* The entry into which pNew is inserted */
      HashElem *pNew         /* The element to be inserted *///被插入的元素
    ){
      HashElem *pHead;       /* First element already in pEntry */
      if( pEntry ){/*determine the pEntry is empty,*///Elaine debug
        pHead = pEntry->count ? pEntry->chain : 0;/*如果pEntry不空，则为pEntry结构赋值*/
        pEntry->count++;
        pEntry->chain = pNew;
      }else{
        pHead = 0;//如果pEntry为0，则pHead为0
      }
      if( pHead ){//判断pHead的值
        pNew->next = pHead;//pNew的next指向pHead
        pNew->prev = pHead->prev;//pNew的prev指向pHead的prev
        if( pHead->prev ){ pHead->prev->next = pNew; }//pHead的prev的next指向pNew
        else             { pH->first = pNew; }
        pHead->prev = pNew;
      }else{
        pNew->next = pH->first;//如果pHead为0，则pNew的下一个指向pH的first
        if( pH->first ){ pH->first->prev = pNew; }//pH的first的prev指向pNew
        pNew->prev = 0;
        pH->first = pNew;
      }
    }

伪代码：
<br></br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果pH的htsize成员变量不为0，即使用哈希表存放数据项，那么使用strHash函数计算桶号h；如果pH的htsize成员变量为0，那么h=0；根据h和键值在哈希表中找键值为pKey的入口项，如果存在，那么使用data替换原数据值，返回原数据值；如果不存在，根据pKey和data组建一个入口项元素pNew；准备插入哈希表：
<br></br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;先哈希表是否需要扩容，如果需要，先将哈希表扩容，同时重新计算h的值；将pNew插入哈希表需要查看哈希表当前的状态，是链表存放还是哈希表存放；如果pH的htsize等于0，则为链表存放，将pNew插入first链表表头即可；如果pH的htsize不等于0，则使用哈希表存放，将链表插入到对应桶链表的表头即可；需要注意的是桶链表和first链表的关系；首先找到桶链表的表头，如果桶中当前没有元素，那么将对应桶的链表头指向pNew，然后将pNew插入到first链表的表头,将对应桶含有的元素数量增加；如果对应桶中已经有元素，即桶的链表头不为空，那么将pNew插入到桶的链表头即可，根据刚刚插入第一个元素时看到的桶链表和first链表的关系可知，此时pNew也在first链表中。


    void *sqlite3HashFind(const Hash *pH, const char *pKey, int nKey){
      HashElem *elem;    /* The element that matches key *//*匹配的元素*/
      unsigned int h;    /* A hash on key */

      assert( pH!=0 );/*pH!=0为真，继续执行，否则推出执行*/
      assert( pKey!=0 );/*pKey!=0为真，继续执行，否则推出执行*/
      assert( nKey>=0 );/*nKey!=0为真，继续执行，否则推出执行*/
      if( pH->ht ){
        h = strHash(pKey, nKey) % pH->htsize;
      }else{
        h = 0;
      }
      elem = findElementGivenHash(pH, pKey, nKey, h);
      return elem ? elem->data : 0;
    }
<br></br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3HashFind函数：在哈希表中查找指定的入口项；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数参数：<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;pH：Hash结构指针，待操作哈希表；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;pKey：字符指针，键值；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nKey：整型，键值长度；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回值：数据值或NULL；<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;伪代码：<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;根据pKey计算哈希值，得到桶号h；如果pH->ht为空，即使用链表实现，那么h=0；如果pH->ht为空，那么遍历first链表，找到键值与pKey相同的入口项；如果如果pH->ht不为空，找到对应的桶链表表头，遍历桶链表，找到键值与pKey相同的入口项；注意：桶链表是first链表的一部分，即不是所有的桶链表的表尾部都是以空结束，所以使用桶的count来控制桶链表的遍历；至于哈希表的扩容，需要所有的元素重新插入。
