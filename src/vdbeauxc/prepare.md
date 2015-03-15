# vdbe准备阶段
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该阶段需要为vdbe添加各种操作码为vdbe的执行做准备。准备阶段涉及到函数有
（1） Sql语句设置函数
该功能由函数void sqlite3VdbeSetSql(Vdbe *p， const char *z， int n， int isPrepareV2)实现。
该函数为vdbe设置值为z的SQL语句。该语句长度为n，isPrepare作为标记参数。
<br/>
（2）操作码添加函数及操作码数组扩展函数
操作码数组扩展功能由函数static int growOpArray(Vdbe *p)实现。
该函数是为VDBE扩展其操作码数组大小。其数组空间是逐渐扩展的。在该函数执行过程中，可能由于内存不足导致调整失败，此时程序会返回SQLITE_NOMEM。Vdbe原有操作码数组及操作码数量保持不变。这样可以保证在VDBE空闲时能够正确释放空余空间。<br/>
操作码添加功能由以下函数实现:<br/>
int sqlite3VdbeAddOp3(Vdbe *p， int op， int p1， int p2， int p3) //对应于操作码的操作数为3个的情况
<br/>
int sqlite3VdbeAddOp0(Vdbe *p， int op) //对应于操作码的操作数为0个的情况
<br/>
int sqlite3VdbeAddOp1(Vdbe *p， int op， int p1) //对应于操作码的操作数为1个的情况
<br/>
int sqlite3VdbeAddOp2(Vdbe *p， int op， int p1， int p2) //对应于操作码的操作数为2个的情况
<br/>
int sqlite3VdbeAddOp4(
  Vdbe *p，            /* Add the opcode to this VM */<br/>
  int op，             /* The new opcode */<br/>
  int p1，             /* The P1 operand */<br/>
  int p2，             /* The P2 operand */<br/>
  int p3，             /* The P3 operand */<br/>
  const char *zP4，    /* The P4 operand */<br/>
  int p4type          /* P4 operand type */<br/>
)  //对应于操作码的操作数为3个，但是有特定的操作码类型的情况<br/>

操作码添加函数主要是由sqlite3VdbeAddOp3实现，其他情况除了sqlite3VdbeAddOp4需要为操作码增加操作类型外，均调用sqlite3VdbeAddOp3进行处理。<br/>
参数p是指向vdbe的指针，op是需要添加的操作码，p1、p2、p3分别对应操作数1、操作数2、操作数3，zP4如果参数p4type>=0，那么zP4是动态的，它是由一个sqlite3_malloc()分配的内存字符串。如果p4type ==0，P4的操作数由zP4确定，并且包含第一个null byte。如果p4type >0，那么P4的操作数是zP4的前n+1byte；如果p4type ==P4_KEYINFO，那么zP4代表一个指向KeyInfo结构的指针。这个指针指向的是由sqlite3_malloc()分配的KeyInfo结构的内存副本。当vdbe销毁时，该空间将被释放。如果是p4type是其他值（P4_STATIC， P4_COLLSEQ等），表示zP4指向的是在vdbe生命周期中存在的一个字符串或结构。在这种情况，直接复制该指针即可；如果addr<0，那么将P4设置为刚插入的指令。<br/>
（3）添加操作码解析模式函数
该功能主要由函数void sqlite3VdbeAddParseSchemaOp(Vdbe *p， int iDb， char *zWhere)实现。
该函数是为了处理sqlite3VdbeAddOp4()中需要标记所有B树的情况，zWhere中必须包含内存分配条件，该函数必须能配置内存。<br/>
（4） Label标签创建以及标签填补(分配地址)函数
Label标签创建功能主要由函数int sqlite3VdbeMakeLabel(Vdbe *p)实现。
该函数实现了为一个即将编码的指令创建一个新的符号标签的过程。标签类似于占位符(个人理解)。起始阶段该标签为负值。它可以被作为P2的操作数。当该标签被分配一个地址时，vdbe会扫描其操作链表并将P2的值设为该地址。vdbe可以辨别出P2的值是一个标签，因为P2的值不应该为负数。因此，在地址被分配前，如果P2的值为负数，则该值为一个标签。该函数返回标签的值。<br/>
创建标签后需要填补标签，这时填补标签的过程其实就是给标签分配地址的过程。<br/>
填补标签功能由函数void sqlite3VdbeResolveLabel(Vdbe *p， int x)和static void resolveP2Values(Vdbe *p， int *pMaxFuncArgs)实现。
前一个函数主要是处理除了P2操作数以外的标签分配地址，该地址是下一个指令插入的地址。标签由参数x提供，参数x必须从sqlite3VdbeMakeLabel中获取，即在函数分配函数执行前必须执行sqlite3VdbeMakeLabel。<br/>
函数resolveP2Values依次检查跳转指令中P2的值是否为负数。如果为负数，则这些值为标签。为这些标签分配相应的地址(相应的非零值)。该函数在所有操作数被插入后被调用。<br/>
参数pMaxFuncArgs表示P2中OP_Function， OP_AggStep o或者 OP_VFilter 操作码的最大值。它被作为一个参数传递给sqlite3VdbeMakeReady()来设置vdbe的apArg[]数组的大小。Op中opflags域对vdbe所有操作码都有效。可以设置为OP_Function等OP_XX之类。如果支持虚表，则可以设置为OP_VUpdate之类。<br/>
（5）添加操作码链函数
该功能主要由函数int sqlite3VdbeAddOpList(Vdbe *p， int nOp， VdbeOpList const *aOp)实现。
该函数实现了将操作码链aOp加入到操作堆栈中并返回第一操作码的地址。nOp代表链中元素的个数。
