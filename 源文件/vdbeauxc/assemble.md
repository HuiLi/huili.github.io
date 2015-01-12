# 配置函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数void sqlite3VdbeRunOnlyOnce(Vdbe *p)
该函数保证vdbe只执行一次，它是通过将vdbe结构中的runOnlyOnce设置为1来实现的。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数int sqlite3VdbeCurrentAddr(Vdbe *p)
该函数返回下一个指令的插入地址。即当前的操作码的数量。因为操作码的数量从0开始计算，当前数量即是下一操作码的插入地址。<br>
函数void sqlite3VdbeUsesBtree(Vdbe *p， int i)
向vdbe声明使用db->aDb[i]指向的B树。在语句准备阶段，vdbe需要知道所有需要使用的附加数据库。这些数据库的掩码保存在p->btreeMask中。p->lockMask的值是p->btreeMask中需要上锁数据库的集合。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数void sqlite3VdbeEnter(Vdbe *p)
如果sqlite在编译时选择支持cache共享模式，这种模式必须保证线程安全。该函数获得vdbe相关的BtShared结构的互斥量。并这些互斥量作为参数传递给VM。在函数执行过程中，将设置BtShared。db成员中每个BtShared结构的互斥量来确保所有需要调用的句柄如果可以调用都被调用。如果sqlite不是线程安全的，但是支持cache共享模式， 那么sqlite3BtreeEnter()会被该函数调用来设置BtShared。db成员中每个与VM相关的BtShared结构的句柄。如果SQLite不是线程安全的也不支持cache共享模式，那么该函数不执行。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
p->btreeMask是语句p中所有涉及到的B树的位掩码。N是p->btreeMask中使用cache共享模式的B树的bit数。所以该函数的运行时间是N*N，一般情况下，N很少大于1，所以该函数时间代价并不高。
