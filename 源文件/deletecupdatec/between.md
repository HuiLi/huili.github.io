# between
<P>
<img src="pic11.JPG"/>
<P>
操作符BETWEEN…AND在where子句中使用，作用是介于两
个值之间的数据范围
IN是指某个值是否存在于某一个结果集中
SELECT ProductID, ProductName FROM Northwind.dbo.Products WHERE CategoryID IN (1, 4, 5);
SELECT pub_name FROM publishers WHERE pub_id
  NOT IN (SELECT pub_id FROM titles WHERE type = 'business');
