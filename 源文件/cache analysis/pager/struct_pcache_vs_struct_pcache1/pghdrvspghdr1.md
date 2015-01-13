# Struct PgHdr 与Struct PgHdr1
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Struct PgHdr VS Struct PgHdr 1：Struct PgHdr 是页面的头部，用来控制缓冲区中页面；Struct PgHdr1每个缓冲区入口，可以大致理解为：通过它，可以获得数据库页面的渠道。
    <img src="../../p33.png"/>
                     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;图 3.3 Struct PgHdr源码截图
    <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;PgHdr只对页面调度程序模块可见，对B+树模块和更高层次的模块均不可见。该页面头有很多控制变量。Pgno变量确定了它所描述的数据库页面的页面号。如果页面被写入到回退日志中，injournal变量就是真。如果日志在回写该页面到数据库文件前需要刷新，needSync就是真。(文件上的刷新将文件更改的部分传递到磁面上。)如果页面被修改了，并且新值还没写回到数据库文件中，dirty变量就是true。如果页面在当前状态的日志中，inStmt变量就是真。在页面上的nRef变量就是引用计数。如果nRef的值比0大，那么页面在用，并且我们也称页面被固定；反之，页面未固定而且空闲。pNextFree和pPrevFree用来连接所有的空闲页。pDirty指针连接了所有的脏页面。另，一个空闲页面也可能是脏页面。
 <img src="../../p34.png"/>
                       &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;图 3.4 Struct PgHdr1源码截图

