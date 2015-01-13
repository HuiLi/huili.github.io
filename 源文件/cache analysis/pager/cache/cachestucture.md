# cache内存结构
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有了以上的认识，我们再来看一下cache的内部结构：一般而言，为了加速搜索缓存，当前保存在缓存中项被很好的组织起来了。SQLite使用哈希表来组织缓存好的页面，并且使用页面槽在表中保存数据。缓存是全相关的，也就是说，任何槽都能够用来存储任何页面。该哈希表初始化为空。当页面请求增加时，页面调度程序创建新的槽，并把他们插入到哈希表中。每个缓存拥有的槽数目是有最大上限的。（只要本地操作系统允许应用程序空间增长，内存内数据库就没有这个限制。）

<img src="../../p36.png"/>
                          &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;图 3.6 page cache的组织形式
    <br>
    <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如图3.6所示，aHash实际是一个Hash数组，装载*PgHdr1（缓冲区入口），nHash个插槽，aHash的大小是在SQLite库编译时确定的。而每个页面链表则通过PgHdr（页面头）组织、管理。页面镜像被存储在PgHdr之后。镜像后面跟着的是被B+树模块用以维持页面特性控制信息的一部分私有数据。（绿色框部分：内存中数据库没有日志文件，所以他们的复原信息被记录在内存内对象中。指向那些对象的指针被存储在私有数据后面：这些指针只能被页面调度程序使用。）在缓存中的这个空间被初始化为0，当页面调度程序将该页带入缓存中时。
 </pre>
