# 相关修改函数
（1）操作数修改函数<br/>
void sqlite3VdbeChangeP1(Vdbe *p， u32 addr， int val)<br/>
void sqlite3VdbeChangeP2(Vdbe *p， u32 addr， int val)<br/>
void sqlite3VdbeChangeP3(Vdbe *p， u32 addr， int val)
操作数的修改由以上函数完成。<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数基本结构相似，都是修改对应P1、P2、P3的值。其中P1用于当较大的程序载入sqlite3VdbeAddOpList生成的静态数组后，需要对该数组进行轻微修改时，P2一般存放跳转指令的目的地址。P3无明显定义。
<br/>
（2）其他修改函数
void sqlite3VdbeChangeP4(Vdbe *p， int addr， const char *zP4， int n)
void sqlite3VdbeChangeP5(Vdbe *p， u8 val)
void sqlite3VdbeChangeToNoop(Vdbe *p， int addr)
void sqlite3VdbeJumpHere(Vdbe *p， int addr)
函数sqlite3VdbeChangeP5用于保存最近添加的操作码。
函数sqlite3VdbeChangeP4为特定的指令修改P4的值，该函数在一个大型程序载入一个由sqlite3VdbeAddOpList生成的静态操作码数组后需要进行轻微时比较有用。
对于P4，如果参数n>=0，那么P4操作数是动态的，它是由一个sqlite3_malloc()分配的内存字符串。如果n==0，P4的操作数由zP4确定，并且包含第一个null byte。如果n>0，那么P4的操作数是zP4的前n+1byte。如果n==P4_KEYINFO，那么zP4代表一个指向KeyInfo结构的指针。这个指针指向的是由sqlite3_malloc()分配的KeyInfo结构的内存副本。当vdbe销毁时，该空间将被释放。如果是n是其他值（P4_STATIC， P4_COLLSEQ等），表示zP4指向的是在vdbe生命周期中存在的一个字符串或结构。在这种情况，直接复制该指针即可。
如果addr<0，那么将P4设置为刚插入的指令。
函数sqlite3VdbeChangeToNoop将地址为addr的操作码类型设置为OP_Noop。
函数sqlite3VdbeJumpHere修改P2的地址值为addr，用于跳转到下一个指令。
