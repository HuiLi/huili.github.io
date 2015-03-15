# Skip-Scan优化
普通的索引规则只有在WHERE子句中约束了最左边的索引列才起作用。然而，有些情况下——即使最初的几个索引列都没有在WHERE子句中使用，但而后的几列都包含在WHERE子句中，那么SQLite还是可以使用索引的。它的要求是需要进行ANALYZE分析，并且最左边的索引列有许多的重复值时，才可以使用基于索引的Skip-Scan。<br>
<pre>例如：
CREATE TABLE people(
 name TEXT PRIMARY KEY,
 role TEXT NOT NULL,
 height INT NOT NULL, -- in cm
 CHECK( role IN ('student','teacher') )
);
CREATE INDEX people_idx1 ON people(role, height);
</pre>
people表中的role字段只有"student"和"teacher"两种取值，虽然role被加上了索引，但其并不具有选择性，因为它只包含两种取值。<br>
考虑下面这个查询：
<pre>
SELECT name FROM people WHERE height>=180;
</pre>
按照普通的索引规则，会认为索引不可用，实际上SQLite会使用索引。SQLite会把上述查询概念的转化为：
<pre>
SELECT name FROM people
 WHERE role IN (SELECT DISTINCT role FROM people)
AND height>=180;
或是这种：
 SELECT name FROM people WHERE role='teacher' AND height>=180
UNION ALL
 SELECT name FROM people WHERE role='student' AND height>=180;
 </pre>
这样索引就可以使用，这样也会比全表扫描要快一些。
注意：Skip-Scan只对重复值大于或等于18时有用，如果没有分析数据库，那么Skip-Scan就不会永远被使用。
