#1.0 概述
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Rtree是为行查询所设计的一种特殊索引。Rtree一般是用在地理信息系统中查询最小或最大xy坐标的矩形框。给出一个查询矩形，Rtree能快速找到所有包含在矩形中或者与矩形重叠的条目。在Cad系统中很容易扩展到3维索引。Rtree在时域查询中也有涉及。比如，如何查询数据库中开始时间和结束时间中的大量记录，Rtree可以迅速的找到在给定时间段内的所有活跃事务，或者在特殊时间段内开始的所有事务，或者所有在特殊时间段内开始并结束的所有事务，等等。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Rtree的概念源于Toni Guttman：Rtree：A Dynamic Index structure for spatial Searching，Proc.1984 ACM SIGMOD International Conference on Management of Data，pp47-55.在SQLite中细化并实现了Guttman的算法，也就是Norbert Beckmann, Hans-Peter Kriegel，Ralf Schneider，Bernhard Seeger:The R-Tree:An Efficient and Robust Access Method for Points and Rectangles.SIGMOD Conference 1990:322-331中描述的R*tree。

