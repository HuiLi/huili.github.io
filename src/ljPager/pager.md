# Pager概述
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;B-tree和pager是相连接的，并且在事务和锁上起着关键作用。一个连接可以有多个数据库对象---一个主要的数据库以及附加的数据库，每一个数据库对象有一个B-tree对象，一个B-tree有一个pager对象
