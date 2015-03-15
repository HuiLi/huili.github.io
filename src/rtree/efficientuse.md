#4.0 有效使用Rtree
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认情况下，Rtree用32bit浮点型数据存放坐标值。当一个坐标值不能用浮点型数据较好的表示时，较小的坐标值将向下取整，较大的坐标值将向上取整。这样一来，区域可能比指定区域略大，不会小，这刚好满足了应用查询与指定区域重叠的区域记录。规整区域记录可能导致查询到一些额外的区域记录刚好和指定区域交界。但是查询重叠区域不是丢失任何一个有效的记录。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;然而，对于包含类型的查询，取整的操作意味着结果集中有些记录由于取整后与指定记录边界重合而被舍弃。为避免这种情况，应用应该通过在每个维度中向下取整较小坐标，向上取整较大坐标来略微扩展查询区域的大小（0.000012%）。<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Rtree索引存放的信息只有对象的ID和对象的边界大小。附加的信息需要存放在其他的表上，并通过主键与Rtree索引关联。对于以上例子，可以通过以下方式创建一个辅助表：

	CREATE TABLE demo_data(
     id INTEGER PRIMARY KEY,  -- primary key
     objname TEXT,            -- name of the object
     objtype TEXT,            -- object type
     boundary BLOB         -- detailed boundary of object
    );
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在这个例子中,demo_data.boundary属性用来存储精确的二进制坐标数据。R树索引只保存了对象区域矩形的轴对齐边界线。R树只保存了对象边界区域的近似值。所以通常情况下，R树索引用来缩小候选值的范围。然后对每个候选值进行详细的验证是否满足条件。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;关键：一个R树索引并不能提供准确的数据，仅仅只是用来降低搜索范围。建立demo_data.boundary属性来保存对象的准确坐标值需要用到sqlite3_create_function()接口来创建自定义contained_in和overlaps函数来验证“包含”和“重叠”。可以认定“contained_in”和“overlaps”函数是查询速度较慢的函数所以不会经常调用。因此，查询包含在the North Carolina 12th District区域的所有记录，可以这样做：

    SELECT objname FROM demo_data, demo_index
     WHERE demo_data.id=demo_index.id
     AND contained_in(demo_data.boundary, :boundary)
     AND minX>=-81.0 AND maxX<=-79.6
     AND minY>=35.0 AND maxY<=36.2;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在上面查询中，可以假定the 12TH district区域的准确坐标绑定在boundary属性中。
上述查询是怎样进行的：Rtree运行在外层循环中，找到所有经度在-81..-79.6，纬度在35.0..36.2中的所有记录。对于每个搜索到的对象，SQLITE在demo_data表中找到与之对应的记录并找到对应的属性值，用contained_in函数验证是否满足条件。如果满足则返回。
下面查询语句不使用R树索引会返回同样的结果：

    SELECT objname FROM demo_data
    WHERE contained_in(demo_data.boundary, :boundary);
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;后面查询的问题在于函数contained_in会应用在demo_data所有数百万的记录上。R树索引的运用大大降低了函数contained_in的调用次数，R树索引并没有找出准确值，但是它大大降低了搜索空间。
