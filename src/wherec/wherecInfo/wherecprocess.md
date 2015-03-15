# where.c的执行过程
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;where.c中首先把传递的where子句通过指定的op（可以是AND也可以是OR）进行分隔，把分隔后的语句放入到slot[]数组中，然后分别根据slot[]中的where子句别选择相应的优化操作，选择正确的索引。然后生成相应的VDBE编码，把数据清除并结束where操作。
<pre>
例如:如果SQL是:
       SELECT * FROM t1, t2, t3 WHERE ...;
 那么会概念地生成以下代码:
      foreach row1 in t1 do       \
        foreach row2 in t2 do      |-- 由sqlite3WhereBegin()生成
          foreach row3 in t3 do   /
            ...
          end                     \
        end                        |-- 由sqlite3WhereEnd()生成
      end                         /
</pre>
where.c的执行基本流程如图：
<img src="process.png"/>
