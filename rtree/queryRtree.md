#3.3查询一个R*Tree索引
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;RTree索引不利于任何有效的查询。但是RTree的实现是为了两种类型的查询更加快捷有效。第一，有效的查询主键：

    SELECT * FROM demo_index WHERE id=1;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当然，一个普通的SQLite表也可以有效的查询整型主键，所以在这以前这不是问题。使用R*Tree的真正原因是可以有效的处理范围查询。找到所有包含在Charlotte，North Carolina内的邻近区域索引：

    SELECT id FROM demo_index
     WHERE minX>=-81.08 AND maxX<=-80.58
       AND minY>=35.00  AND maxY<=35.44;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;尽管R*Tree包含了数百万的记录，上述查询也可以快速定位到id 1记录。这是一个包含在区域之内的查询实例。Rtree同样支持重叠区域查询。比如，找到所有与Charlotte区域有重叠区域的查询：

    SELECT id FROM demo_index
     WHERE maxX>=-81.08 AND minX<=-80.58
     AND maxY>=35.00  AND minY<=35.44;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;第二个查询会找到记录1（the SQLite Office）包含在查询的框架范围中，和the 12th congressional District未包含在查询的框架范围中但和查询范围有重叠区域。没有必要为了让查询更加有效而给所有的坐标都加上约束。比如，查询所有与35th区域有重叠区域的记录：

    SELECT id FROM demo_index
     WHERE maxY>=35.0  AND minY<=35.0;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;但是，一般来说，R*tree约束越多，查询区域的大小越精确，查询速度越快。

