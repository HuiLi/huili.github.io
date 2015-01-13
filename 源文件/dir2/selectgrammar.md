# SELECT的语法图
 <img src="../select1.png"/>
 上图只是语法的书写顺序，而select的执行顺序：
1.	  FROM
2.	  ON
3.	  JOIN
4.	  WHERE
5.	  GROUP BY
6.	  WITH CUBE or WITH ROLLUP
7.	  HAVING
8.	  SELECT
9.	  DISTINCT
10.	  ORDER BY
11.	  TOP
先进行From，将表进行连接，查询出的是个“笛卡尔积”集，然后交给on子句连接过滤，再交给where进行条件过滤，再进行Group分组，再进行having过滤分组，再进行select输出，如果有order，limit，offset再对结果集进行排序，再返回限定行数和限定偏移量的结果集。
