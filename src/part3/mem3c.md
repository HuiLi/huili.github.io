# Mem3.c功能概述
我们都知道，SQLite采用了模块的设计，它由三个子系统，包括8个独立的模块构成。三个子系统分别是core（内核）、compiler（编译器）、Backend（后端）。其中，内核包含了编译器。而8个独立模块分别是interface（接口）、tokenizer（分词器）、parser（分析器）、code generator（代码生成器）、virtual machine（虚拟机）、B-tree（b树）、Pager（页）、OS-interface（操作系统接口）。每个模块都有自己特定的功能。而内存分配几乎在每个功能下都要用到，sqlite实现内存分配的几个代码文件是mem0—5.c，malloc.c负责内存管理。而我主要对mem3.c进行深入分析。

Mem3.c是sqlite数据库一种内存分配子系统的实现，它引入了chunk和block的概念。不使用malloc()函数进行内存分配，使用者在调用sqlite3_initialize()初始化函数之前申请内存空间，该内存空间由函数xMalloc() and xRealloc() 产生和分配，在内存初始化之前就已经分配好。当调用内存初始化函数sqlite3_initialize()时，申请的内存空间就被固定下来，不能改变。这块固定的内存空间，就是free chunk。该内存子系统只有在SQLITE_ENABLE_MEMSYS3宏被定义时才被组建进库。需要注意的是，定义了这个宏，只代表该内存分配可用，并不代表系统库将默认使用它，只有在调用sqlite3_config()时，该内存池才被激活。Mem3.c解决了内存泄漏的问题。
