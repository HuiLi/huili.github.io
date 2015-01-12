# 用户自定义函数
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可以使用以下的方法来创建用户自定义的SQL函数:

       typedef struct sqlite3_value sqlite3_value;
       int sqlite3_create_function(
         sqlite3 *,
         const char *zFunctionName,
         int nArg,
         int eTextRep,
         void*,
         void (*xFunc)(sqlite3_context*,int,sqlite3_value**),
         void (*xStep)(sqlite3_context*,int,sqlite3_value**),
         void (*xFinal)(sqlite3_context*)
       );
       #define SQLITE_UTF8     1
       #define SQLITE_UTF16    2
       #define SQLITE_UTF16BE  3
       #define SQLITE_UTF16LE  4
       #define SQLITE_ANY      5

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nArg 参数用来表明自定义函数的参数个数。如果参数值为0，则表示接受任意个数的参数。用 eTextRep 参数来表明传入参数的编码形式。参数值可以是上面的五种预定义值。SQLite3 允许同一个自定义函数有多种不同的编码参数的版本。数据库引擎会自动选择转换参数编码个数最少的版本使用。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;普通的函数只需要设置 xFunc 参数，而把 xStep 和 xFinal 设为NULL。聚合函数则需要设置 xStep 和 xFinal 参数，然后把 xFunc 设为NULL。
