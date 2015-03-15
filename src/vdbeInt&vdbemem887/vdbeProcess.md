#  VDBE的运行
<img src="vdbe.jpg">



 aOp数组保存有SQL经过编译后生成的所有指令。指令计数器(PC)每一个vdbe都有一个程序计数器，用来保存初始的计数器值。经过编译器生成的vdbe指令最终都是由解释引擎解释执行的

<img src="opc.png">

上图是一个SQL语句在通过命令成成的字节码指令。在指令中可以看到Transaction指令表示开始一个事务，Commit或Rollback结束一个事务，VerifyCookie检验数据库模式的版本等等。
OpenWrite指令在P1（这儿为0）上即examp表上打开一个读写指针。

备注：B-tree和pager的使用
sqLite的数据都存放在数据库页当中。数据库页当中存储着大量信息（包括记录，字段和索引等）。B-tree负责将页组织为树状结构，方便搜索。Pager根据B-tree的要求对磁盘进行读写操作。（另一种说法：每一个B-tree都对应一个表或者索引。）
总之，每一条SQL语句都会被编译为一系列操作码供VDBE执行。
