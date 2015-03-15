# Lemon语法分析器模板文件
**1、Lemon简介**

Lemon的主要功能就是根据上下文无关文法(CFG)，生成支持该文法的分析器。程序的输入文件有两个： (1) 语法规则文件；                    (2) 分析器模板文件   Lemon中所涉及的一些重要概念如下： 词法分析器（Tokenizer）
当执行一个包含SQL语句的字符串时，接口程序要把这个字符串传递给tokenizer。Tokenizer的任务是把原有字符串分割成一个个标识符（token），并把这些标识符传递给解析器。Tokenizer是用手工编写的，在C文件tokenize.c中。
    在这个设计中需要注意的一点是，tokenizer调用parser。熟悉YACC和BISON的人们也许会习惯于用parser调用tokenizer。SQLite的作者已经尝试了这两种方法，并发现用tokenizer调用parser会使程序运行的更好。YACC会使程序更滞后一些。

公共接口（Interface）
	SQLite库的大部分公共接口由main.c, legacy.c和vdbeapi.c源文件中的函数来实现。
	这些函数依赖于分散在其他文件中的一些程序，因为在这些文件中它们可以访问有文件作用域的数据结构。sqlite3_get_table()例程在table.c中实现，sqlite3_mprintf()可在printf.c中找到，sqlite3_complete()则位于tokenize.c中。Tcl接口在tclsqlite.c中实现。SQLite的C接口信息可参考http://sqlite.org/capi3ref.html。

语法分析器（Parser）
 语法分析器的工作是在指定的上下文中赋予标识符具体的含义。SQLite的语法分析器使用Lemon LALR(1)分析程序生成器来产生，Lemon做的工作与YACC/BISON相同，但它使用不同的输入句法，这种句法更不易出错。Lemon还产生可重入的并且线程安全的语法分析器。Lemon定义了非终结析构器的概念，当遇到语法错误时它不会泄露内存。驱动Lemon的源文件可在parse.y中找到。

**3.2 相关数据结构**

**3.2.1 Acttab数据结构**

构建yy_action[]表时候，要用到acttab这个结构，这个结构中包含的部分定义如下： nAction-在aAction[]动作表中，元素实际使用数量的多少 nActionAlloc-aAction[]动作表的总数量，或者说是数组的长度 lookahead-先行符号，它的值是文法符号在符号表中的序号（） action-相应某一先行符号的动作序号 aActtion-具有lookahead和action两个域结构的链表结构，也就是yy_action[]动作表

**
3.2.2一些跟状态量相关的宏定义**

define YY_NO_ACTION      (YYNSTATE+YYNRULE+2)  (无动作)定义成一个常数，其大小是状态数量与产生式数量之和再加2 #define YY_ACCEPT_ACTION  (YYNSTATE+YYNRULE+1) （接受）也定义成一个常数，其大小是状态数量与产生式数量之和再加1 #define YY_ERROR_ACTION   (YYNSTATE+YYNRULE) （错误动作）定义成一个常数，期大小是状态数量与产生式数量之和


**3.2.3 模板文件中的数组**

在模板文件中我们要生成一系列的数组，并且这些数组的元素只能是整数，这样大大压缩了语法分析器占用的空间和提高了它的工作效率。
我们的目标是建立一个一维的yy_action[]数组。当我们用表示分析器状态（satate）的整数和表示先行符号（lookahead）的整数，向这个数组检索时，它应该返回对应
那个动作（action）,不过此时的动作也用某个整数N来表示。
 假定，从这个yy_action[]动作数组中检索，获得了代表某个动作的整数数值是N，则我们如何判断它是移进、规约、接受还是错误呢？N数值代表的动作行为可由如下的数值区间决定。
    当N具有下列数值范围时，表示的动作是移进：
       0 <= N < YYNSTATE  （这里的YYNSTATE是状态的总数目）
    并且，这时把先行符压入语法分析栈，然后进入状态N。    当N具有下列的数值范围时，表示的动作是规约：
       YYNSTATE <= N < YYNSTATE+YYNRULE （这里的YYNSTATE是状态的总数目）
    当N具有下列数值时，表示语法反洗过程中发现一个错误：
       N == YYNSTATE+YYNRULE
    当N具有下列数值时，表示语法分析器到达接受状态。语法分析至此完毕，并且没有错误：
       N == YYNSTATE+YYNRULE+1
    当N具有下列数值时，表示这是不可能有的动作。这意味着yy_aciton[]数组中这些元素上没有动作。
      N == YYNSTATE+YYNRULE+2
    由于我们建立的动作表示一维表（编译原理中常为二维表，一维表示状态，另一维表示先行符合非终结符的产生式），当得到一个状态s和一个先行符x时，可以用如下公式计算，从数组中找到进行的动作N；
      yy_action[ yy_shift_ofst[S] + X ]
   关于上式的解释：由yy_shift_ofst[S]这个公式，通过S在yy-shift_ofst[]数组中进行搜索，可以得到S的状态所有的那一行偏离yys_aciton[]数组的绝对位置（改绝对位置为整数）。
     X先行符（在数组中也是一个整数），是符号的序号，同上述绝对位置相加得到的整数，就是yy_aciton数组某个元素的索引值。

    如果用yy_shift_ofst[S]+X计算得出的索引值大于yy_action[]数组的长度，说明这个状态S中不可能含有这样的X，所以不可能有动作。
    如果我们用yy_lookahead[yy_shift_ofst[S]+X](yy_lookahead是一个数组，可以用来判断某个X的先行符是不是与某个S状态相匹配)
	计算出来的值不等于X本身的值，说明此X先行符被错误使用了，它与这个S状态不匹配，也就不会被接纳进语法分析栈中。

    当yy_shift_ofst[S]的值等于YY_SHIFT_USE_DFLT默认动作时，但是这时的动作都不含在动作表中，此时调用的是yy_default[S]中指定的动作。

    当先行符时非终结符时，则应该运用yy_reduce_ofst[]数组，而不再使用yy_shift_ofst[],同时也应该用YY_REDUCE_USER_DFLT代替YY_SHIFT_USE_DFLT


   下面是对各个数组的简单描述，这些数组在代码运行的过程中自动创建：

    yy_action[]      包含所有动作的一维数组

    yy_lookahead[]   包含了所有合法的克星的先行符的一维数组

    yy_shift_ofst[]  一维数组。对于每一个状态S，当移进一个终结符X时，用于计算该状态在yy_action[]数组中的绝对位置。

    yy_reduce_ofst[] 一位数组。对于每一个状态S，当一个归约动作后并移进该非终结符X时，用于计算该状态在yy-action[]数组中的绝对位置。

    yy_default[]      一维数组。每一个状态S中的默认（Default）动作。



**3.3 语法分析器接口**

**3.3.1 ParseAlloc（）—构造语法分析器  **

程序在使用Lemon生成的分析器之前，必须创建一个分析器。如下：       void *pParser = ParseAlloc( malloc );     ParseAlloc为分析器分配空间，然后初始化它，返回一个指向分析器的指针。SQLite对应的函数为：      void *sqlite3ParserAlloc(void *(*mallocProc)(size_t)) ParseFree（）— 废弃语法分析器 函数作用：当程序不再使用分析器时，应该回收为其分配的内存，废弃整个语法分析栈，在废弃同时，调用yy_destructor()函数，先废除掉堆栈中的元素。    函数参数（1）指向被废弃语法分析栈的指针p                     （2）用于释放占用内存的函数指针freeProc()

3**.3.2  Parse（**）

—主函数，用于语法分析器的操作           语法分析器最重要的函数，作用是从标记流中，每次接收一个符号，然后在函数内自动地对之进行语法分析
函数参数：

1.第一个参数指向了语法分析栈。

2.第二个参数是符号的大记号yymajor，即此符号的序号

3.第三个参数是此符号的小记号yyminor。即此符号的值

4.第四个参数是一个可选的参数，由进行语法分析的代码编                     写者在语法文件中专门指定的。

**3.3.3  ParseTrace（）**

—追踪语法分析器操作过程
 传入两个参数： 		第一个是要写入的文件，如果为空，则关闭追踪记录            第二个参数是一个字符串，当在向文件（第一个参数决定）写入 时，它在每一行内容的前部都加上这个字符串，以作为某次追踪记录的标记。 ParseTokenName（）—按给定符号的序号，从yyTokenName[]数组中，返回该符号的字符串表示形式。

**3.4 语法分析器动作接口**

Parse()函数，通过该函数的第四个参数，传递和建立一棵语法分析树。 五大部分功能：      1. 对语法分析栈进行初始化      yypParser->yyidx = 0;      2. 关于移进的操作过程          调用yy_find_shift_action()函数，得到对应的动作代号yyyact（整数），如果yyact小于YYNSTATE(状态总数量)，说明此时的操作动作是一个文法符号的进栈移进操作。
3. 接受动作的操作过程         调用yy_accept()函数，进行接受动作的操作，该函数就是把语法分析栈所有剩余的符号进行退栈操作，直至为空（yyidx=-1）    4. 归约动作的操作过程        主要针对yyact值大于等于状态数（YYNSTATE）的情况。        调用yy_find_shift_action()函数返回yyact的值，如果小于YYNSTATE和YYNRULE之和，说明是归约动作的操作。    5. 关于出现错误时的动作操作过程      调用yy_find-shifit_action()函数返回yyact的值为YY_ERROR_ACTION时，表示是出现错误的动作操作
