#3.1创建一个R*tree索引
一个R*tree索引可以用以下方式创建：

    CREATE VIRTUAL TABLE <name>
    USING  rtree(<column-names>);
&lt;name>是你的应用程序所使用的R*tree的索引名
&lt;column-names>是一个由逗号分隔的列链。
实际上将创建3个虚表来存储它的内容：

    <name>_node
    <name>_rowid
    <name>_parent

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这3个虚表就是普通的SQLite表。你可以直接查询表中的记录，尽管可能没有特殊的用处。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可以对这3个表记录进行插入、更新、删除操作，或者删除表，但这样可能会破坏Rtree结构，最好直接忽视这些虚表。了解它们保存了Rtree索引记录就可以了。
比如，建立一个二维Rtree索引用于进行空间查询：

    CREATE VIRTUAL TABLE demo_index USING rtree(
      id,          -- Integer primary key
      minX, maxX,  -- Minimum and maximum X coordinate
      minY, maxY   -- Minimum and maximum Y coordinate
    );
