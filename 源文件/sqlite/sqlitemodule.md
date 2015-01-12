# SQLite模块结构
SQLite 由以下几个主要模块组成：SQL 编译器、内核、后端。

* 接口：接口由SQLite C API组成，程序可以通过API与SQLite数据库进行交互。


* 编译器：分词器（Tokenizer）和分析器（Parser）对执行的SQL语句进行语法检查，然后生成语法树方便VDBE代码生成器使用，然后把语法树传给VDBE代码生成器（Code Generator）处理。代码生成器根据语法树生成VDBE的类汇编语言操作码。


* 后端：由B-树(B-tree)，页缓存管理（pager）和操作系统接口构成。B-tree和Pager共同对数据进行管理。B-tree用与对数据建立索引，它维护着各个页面之间的复杂的关系，便于快速找到所需数据。Pager的主要作用就是通过OS接口在B-tree和硬盘之间传递页面。

![Alt text](picture1-2.png)

Where.c 模块属于上图中Code Generator（VDBE代码生成器），主要进行查询优化及查询处理代码生成。
