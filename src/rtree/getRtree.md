#3.2获取一个R*tree索引
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;传统的Insert，update和delete语句可以像在普通表上一样在Rtree索引上执行。插入一些数据到R*tree索引中，我们可以这样做：

    INSERT INTO demo_index VALUES(
    1,                   -- Primary key                    -- SQLite.org headquarters
    -80.7749, -80.7747,  -- Longitude range
    35.3776, 35.3778     -- Latitude range
    );
    INSERT INTO demo_index VALUES(
    2,                   -- NC 12th Congressional District in 2010
    -81.0, -79.6,
    35.0, 36.2
    );
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;以上记录表示SQLite.org总部的坐标边界框和NC 12th Congressional District in 2010的坐标边界框。

