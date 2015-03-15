# 自动索引
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite会在一定情况下创建一个自动索引用于帮助提高一个查询的效率，这个自动索引值在当前的SQL语句中被使用。SQLite构造自动索引的代价是O(NlogN)（其中N是表中的项目数）。因为做一个全表扫描的代价是O(N)，所以，SQLite只会在希望SQL语句中运行的查找多余logN次时，才被创建。SQLite中自动索引默认是启用的，但SQLite支持手动关闭自动索引，设置automatic_index pragma来使它停用。<p>
程序实现如下：
<img src="autoindex1.png"/>
<p>
选择是否使用自动索引：
<img src="autoindex2.png"/>
