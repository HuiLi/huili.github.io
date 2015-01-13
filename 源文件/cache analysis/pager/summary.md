# 总结
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;通过本次学习，基本弄清楚了Struct PgHdr 、 Struct PgHdr1、 Struct PCache、StructPCache1等结构体间的关系。通过对文件中的结构体以及功能函数的研究，对页面缓存机制有了进一步了解。由于源码涉及大量的指针调用，并且一些宏定义文件在源文件中查不到（应该是版本升级的原因，有的宏定义被封装起来并直接引用），对文件中的一些函数，只能大概理解其功能。<br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在刚开始分析时，先参考了《SQLite权威指南》一书，通过本书对page cache管理的运行机制有了一个模糊的认识，随后通过SourceInsight等分析工具，开始对文件中函数进行逐个分析，明白了各个函数的作用后，思路逐渐明晰起来，对cache的组织结构也有了一个模糊概念。最后通过网上查阅资料，包括查看官方文档、InsideSqlite等对整个cache体系结构有了较为清晰的认识。

