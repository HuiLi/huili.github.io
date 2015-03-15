# 快速使用SQLite
这里简要介绍了SQLite的使用方法.<br>

1.建立一个新的数据库<br>
在shell或者DOS命令行下输入:"sqlite3 test.db". 这将建立一个新的名为"test.db"的数据库文件.(可以使用不同的数据库名)<br>
在命令行提示符中输入SQL命令创建并分配数据库空间.<br>
更多相关内容请参考第X章.<br>

2.编写一个使用SQLite的示例程序<br>
以下是一个演示如何使用SQLite的TCL接口的TCL程序.该程序通过执行SQL语句建立一个由两个参数定义的数据库.首先程序定位并执行第7行sqlite3命令打开并创建一个db对象。通过eval方法将SQL语句绑定到db对象并执行.最后关闭数据库连接.
<pre>
01  #!/usr/bin/tclsh
02  if {$argc!=2} {
03  puts stderr "Usage: %s DATABASE SQL-STATEMENT"
04    exit 1
05  }
06  package require sqlite3
07  sqlite3 db [lindex $argv 0]
08  db eval [lindex $argv 1] x {
09    foreach v $x(*) {
10      puts "$v = $x($v)"
11    }
12    puts ""
13  }
14  db close
</pre>

下面是一个演示如何使用SQLite C/C++接口的C程序.<br>第一个参数表示数据库的名称,第二个参数表示需要执行的SQL语句.
<pre>
01  #include <stdio.h>
02  #include <sqlite3.h>
03
04  static int callback(void *NotUsed, int argc, char **argv, char **azColName){
05    int i;
06    for(i=0; i&lt;argc; i++){
07      printf("%s = %s\n", azColName[i], argv[i] ? argv[i] : "NULL");
08    }
09    printf("\n");
10    return 0;
11  }
12
13  int main(int argc, char **argv){
14    sqlite3 *db;
15    char *zErrMsg = 0;
16    int rc;
17
18    if( argc!=3 ){
19      fprintf(stderr, "Usage: %s DATABASE SQL-STATEMENT\n", argv[0]);
20      return(1);
21    }
22    rc = sqlite3_open(argv[1], &db);
23    if( rc ){
24      fprintf(stderr, "Can't open database: %s\n", sqlite3_errmsg(db));
25      sqlite3_close(db);
26      return(1);
27    }
28    rc = sqlite3_exec(db, argv[2], callback, 0, &zErrMsg);
29    if( rc!=SQLITE_OK ){
30      fprintf(stderr, "SQL error: %s\n", zErrMsg);
31      sqlite3_free(zErrMsg);
32    }
33    sqlite3_close(db);
34    return 0;
35  }
</pre>
