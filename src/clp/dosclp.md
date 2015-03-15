# 命令行模式下的CLP
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;关于命令行模式下的CLP，可以执行如导入/导出数据、返回结果集和一般的批处理工作。使用shell脚本来自动管理数据库是理想的使用方式。从shell（Windows或UNIX）上启动CLP -help，可以看到CLP在命令行模式下的一些功能。
<img src="14.jpg">
1）命令行中的CLP可以接受以下参数：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可选列表；<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;数据库文件名；<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;要执行的SQL命令。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;除了init是指定SQL命令的批处理文件外，绝大部分选项控制输出格式化。数据库文件名是必需的。SQL命令是可选的。<br>
2）数据库管理<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;所有的数据库管理任务都可以在shell和命令行模式下完成。<br>
3）创建、备份和删除数据库<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;数据库的备份有两种方法。第1种是使用.dump，可得到SQL格式的文件。在命令行方式下可如下做：

    sqlite3 test.db .dump > test.sql
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在CLP中可如下做：

    sqlite> .output file.sql
    sqlite> .dump
    sqlite> .exit
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;相应地，导入一个SQL格式备份的数据库可如下做：

    sqlite3 test.db < test.sql
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此处假设test.db不存在。如果它存在，则或许会因为数据库中有同名的对象而出错。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可以用复制的方法得到一个二进制的数据库文件拷贝。但也许在复制之前你想先抽空(vacuum)它，也就是释放数据库文件中未使用的空间，以得到一个更小的数据库文件。可操作如下：

    sqlite3 test.db VACUUM
    cp test.db test.backup
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一般情况下，二进制的备份如不SQL备份兼容性好。尽管SQLite有很好的向上兼容性和各操作系统间文件格式的一致性，但如果想要将备份文件保留很长时间，还是SQL格式保险一些。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当一个数据库你不想再用时，简单地从操作系统中将其文件删除就行了。<br>
4）获得数据库文件的信息<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;按前文所述，获得数据库信息的主要途径是使用sqlite_master视图，它提供一个数据库所包含的所有对象的细节信息。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果你想获得关于物理的数据库结构信息，可以使用一个称为SQLite Analyzer的工具，它可以在SQLite网站上下载得到。SQLite Analyzer可以提供磁盘SQLite数据库的详细技术信息。
