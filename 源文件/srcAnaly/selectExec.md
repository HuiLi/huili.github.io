# 简单select语句的执行过程
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在第二章使用了select查询，下面我们根据简单查询语句的执行过程，主要罗列在处理过程所执行过的函数。每次调用的相关说明并不多，有的只说明关键变量的值，有的简单说明执行过程。主要是调用的太多了，实在没法对每次调用都详细说明。从中了解SQLite的运行机制。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第一层：sqlite3_exec，zSql="select * from student;"。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第二层：sqlite3Prepare，"调用sqlite3RunParser。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第三层：sqlite3RunParser，每处理一个单词，调用一次sqlite3Parser。当语句处理完毕，语句串变为""，最后一次调用sqlite3Parser。在sqlite3Parser中，后部有一个do while循环。循环了好多遍，下面一句也执行了好多遍：yy_reduce(yypParser,yyact-YYNSTATE);
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;yy_reduce中有一个大的switch语句，每次调用执行的分支不同。终于有一遍中调用了sqlite3Select。sqlite3Select是select语句的处理主程序，在其中又经过如下调用层次。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3Select(在select.c中) →sqlite3SelectPrep(在select.c中) →
sqlite3SelectExpand(在select.c中)→sqlite3WalkSelect(在walker.c中) →
selectExpander(在select.c中) →sqlite3LocateTable(在build.c中) →
sqlite3ReadSchema(在prepare.c中)→sqlite3Init(在prepare.c中)。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第四层：sqlite3Init：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;功能：初始化所有数据库文件——主数据库、临时数据库和所有附加的数据库。返回成功码。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;执行：进入第一个循环语句。在循环语句中调用sqlite3InitOne。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;需要注意的是程序中有一小句很重要：db->init.busy = 1；当db->init.busy被设为1时，就不会再有VDBE代码生成或执行。后面就可以在回叫函数中通过执行系统表中的create语句的方式为对象创建内部数据结构而又不会实际地执行这些创建语句。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第五层：sqlite3InitOne：(在prepare.c中)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;功能：读入一个单独数据库文件的schema，并初始化内部的数据结构。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;执行：调用回叫函数sqlite3InitCallback，执行系统表的创建语句，为系统表创建内部数据结构。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3InitCallback：(在prepare.c中)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;功能：本函数是初始化数据库时的回叫程序。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;执行：调用sqlite3_exec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;zSql=CREATE TABLE sqlite_master(<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  type text,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  name text,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  tbl_name text,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  rootpage integer,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  sql text<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第八层：sqlite3Prepare:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;zSql=CREATE TABLE sqlite_master(<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  type text,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  name text,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  tbl_name text,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  rootpage integer,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  sql text<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec，从sqlite3Prepare返回后，执行到sqlite3_step一句。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第八层：sqlite3_step，sqlite3_step是顶层函数，它调用sqlite3Step完成主要工作。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第九层：sqlite3Step，此函数中调用了sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第十层：sqlite3VdbeExec，不执行实际的创建功能，直接返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第九层：sqlite3Step，回到sqlite3Step后，发现sqlite3VdbeExec调用的返回结果为101。rc又与另一个数“与”操作后，值为21，返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第八层：sqlite3_step，rc值在其后的运算中变为101，返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec，rc!=SQLITE_ROW，不回显，退出循环。返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3InitCallback，返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第五层：sqlite3InitOne，继续执行，很多语句之后，遇到调用sqlite3_exec，查询系统表的内容。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3_exec，zSql=SELECT name, rootpage, sql FROM 'main'.sqlite_master。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3Prepare，zSql=SELECT name, rootpage, sql FROM 'main'.sqlite_master，正常返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3_exec，从sqlite3Prepare返回后，在sqlite3_step中经过几层调用到sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3VdbeExec，这次要真正地执行查询功能。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3_exec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;注：仍然在SELECT name, rootpage, sql FROM 'main'.sqlite_master的STEP循环中。从sqlite3_step返回后，rc==SQLITE_ROW，继续处理返回的记录。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在“if( xCallback(pArg, nCol, azVals, azCols) ){”一句中调用sqlite3InitCallback，然后，又调用sqlite3_exec。这里调用sqlite3_exec是要执行系统表中每个对象的创建语句，目的是为这些对象创建内存数据结构。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;zSql=create table student (<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    id int primary key,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    name varchar(10),<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    age int,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;score float );<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第八层：sqlite3Prepare。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;zSql=create table create table student (<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    id int primary key,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    name varchar(10),<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    age int,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;score float );<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec，从sqlite3Prepare返回后，在sqlite3_step中经过几层调用sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第八层：sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec，rc!=SQLITE_ROW，不回显，退出循环。返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3_exec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;注：仍然在SELECT name, rootpage, sql FROM 'main'.sqlite_master的STEP循环中。循环处理下一条记录。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从sqlite3Prepare返回后，在sqlite3_step中经过几层调用sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3VdbeExec。<br><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3_exec，继续，sqlite3_step的返回值为101，rc!=SQLITE_ROW，不回显，退出循环。返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;注：此时sqlite_master表中只有一条记录，至此，已经处理完了。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第五层：sqlite3InitOne：返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第四层：sqlite3Init。
退出第一个循环。又调用sqlite3InitOne。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第五层：sqlite3InitOne：调用sqlite3InitCallback，创建临时数据库的系统表。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3InitCallback：调用sqlite3_exec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec，zSql=CREATE TEMP TABLE sqlite_temp_master。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第八层：sqlite3Prepare，zSql=CREATE TEMP TABLE sqlite_temp_master。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec，从sqlite3Prepare返回后，在sqlite3_step中经过几层调用sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第八层：sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第七层：sqlite3_exec，rc!=SQLITE_ROW，不回显，退出循环。返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第六层：sqlite3InitCallback：返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第五层：sqlite3InitOne：继续执行，调用sqlite3_exec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第四层：sqlite3Init：从sqlite3InitOne返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第三层：sqlite3RunParser：从sqlite3Init经多级返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第二层：sqlite3Prepare，从sqlite3RunParser返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第一层：sqlite3_exec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从sqlite3Prepare返回。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从sqlite3Prepare返回后，在sqlite3_step中调用sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第二层：sqlite3VdbeExec:，这次要真正地执行查询功能。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第一层：sqlite3_exec，从sqlite3VdbeExec返回，rc==100==SQLITE_ROW。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在“if( xCallback(pArg, nCol, azVals, azCols) ){”一句中显示一条记录。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第二层：sqlite3VdbeExec:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第一层：sqlite3_exec:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从sqlite3VdbeExec返回，rc==100==SQLITE_ROW。显示第2条记录。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第二层：sqlite3VdbeExec:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第一层：sqlite3_exec:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从sqlite3VdbeExec返回，rc==100==SQLITE_ROW。显示第3条记录。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第二层：sqlite3VdbeExec:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第一层：sqlite3_exec:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从sqlite3VdbeExec返回，rc==100==SQLITE_ROW。显示第4条记录。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第二层：sqlite3VdbeExec。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;查询执行第一层：sqlite3_exec:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从sqlite3VdbeExec返回，rc!=SQLITE_ROW。定案并退出循环。处理过程结束。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;以上就是简单select语句的执行过程。

