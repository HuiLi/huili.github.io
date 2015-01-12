文件包含用于分配内存的功能，比较字符串和类似的东西。
```
#include "sqliteInt.h"
#include <stdarg.h>
#ifdef SQLITE_HAVE_ISNAN//is not a number
# include <math.h>
#endif
```

如果文件中定义了SQLITE_HAVE_ISNAN，那么此文件就包含sqliteInt.h、stdarg.h、math.h文件，否则只调用sqliteInt.h、stdarg.h文件。

```
/*
** Routine needed to support the testcase() macro.//程序需要支持testcase()宏。
*/

#ifdef SQLITE_COVERAGE_TEST
void sqlite3Coverage(int x){
  static unsigned dummy = 0;/*定义了一个无符号的静态变量，用于保存运算的中间结果*/
  dummy += (unsigned)x;/*将X强制转换成无符号类型*/
}
#endif
```

如果定义了SQLITE_COVERAGE_TEST则执行void sqlite3Coverage函数。


sqlite3IsNaN()
如果浮点指针值不是一个数，返回真。如果编译SQLITE_HAVE_ISNAN，就使用math库的isnan() 函数。否则，工作在大多数系统上，我们有自己的实现。



```
int sqlite3Strlen30(const char *z){
  const char *z2 = z;/*将z的地址赋值给z2*/
  if( z==0 ) return 0;/*如果z为空就返回0*/
  while( *z2 ){ z2++; }/*z2不为空是，将指针z往后移动一位*/
  return 0x3fffffff & (int)(z2 - z);/*z2指向的字符串尾地址减去该字符串首地址，将得到的结果与上0x3fffffff ，以确保返回值永远不会是负数*/
}
```

计算字符串的长度。这些字符串被限制存储在32位有符号整数的低30位。这个返回值永远不会是负数，也不会是大于字符串实际长度的数。对于很长的字符串返回值可能是一个小余真实字符串的长度。

```
void sqlite3Error()
设置sqlite处理数据库时最新的错误代码和字符串.错误的代码被设置为"err_code"。如果错误字符串不为空,字符串zFormat指定了错误字符串在printf函数中的格式。
以下格式被允许
**
**      %s      Insert a string     插入一个整数型
**      %z      A string that should be freed after use     字符串在使用后被释放
**      %d      Insert an integer 插入一个整数
**      %T      Insert a token插入一个令牌
**      %S      Insert the first element of a SrcList  插入 SrcList的第一个元素
```

清除 sqlite处理db时所产生的最新错误,应该调用sqlite3error设置 err_code为 sqlite_ok和zformat为空



```
void sqlite3ErrorMsg 
增加一个错误的信息到 pParse->zErrMsg并添加到 pParse->nErr。以下格式被允许
**      %s      Insert a string     插入一个整数型
**      %z      A string that should be freed after use     字符串在使用后被释放
**      %d      Insert an integer 插入一个整数
**      %T      Insert a token插入一个令牌
**      %S      Insert the first element of a SrcList  插入 SrcList的第一个元素
```

这个函数用于报告任何编译 SQL语句时出现的错误。sqlite3_prepare（）函数做的最后一件事是，复制这个函数存储错误使用到sqlite3Error（）数据库句柄。在执行语句时，函数 sqlite3Error()应该被使用。



sqlite3Dequote
通过去掉引用符，转换一个 SQL引用类型的字符串为常规字符串。在原地完成转换。如果输入不是以一个引用字符开始，那么这个程序就是一个空操作输入的字符串必须是零终止。一个新的零终止字符加到dequoted字符串如果 dequoting   没有 发生或未用引号  引起  的字符串长度，则返回-1.如果dequoting 发生，则以零终止。这一程序被扩展以去除MS接入风格括号各地的标识符。例如：“[A-B-C]”变成“A-B-C”。



```
int sqlite3_stricmp(const char *zLeft, const char *zRight){
  register unsigned char *a, *b;/*定义无符号的寄存器指针变量*/
  a = (unsigned char *)zLeft;/*将zLeft;强制转换成无符号类型的字符型指针变量，并将其地址赋值给a*/
  b = (unsigned char *)zRight; /*将zRight;强制转换成无符号类型的字符型指针变量，并将其地址赋值给b*/
  while( *a!=0 && UpperToLower[*a]==UpperToLower[*b]){ a++; b++; }/*当前指针a不为空，且指针a和b当前指向的内容相同时，a,b同时向低地址位移一位 */
  return UpperToLower[*a] - UpperToLower[*b];
}
int sqlite3_strnicmp(const char *zLeft, const char *zRight, int N){
  register unsigned char *a, *b;
  a = (unsigned char *)zLeft;
  b = (unsigned char *)zRight;
  while( N-- > 0 && *a!=0 && UpperToLower[*a]==UpperToLower[*b]){ a++; b++; }
  return N<0 ? 0 : UpperToLower[*a] - UpperToLower[*b];/*如果N<0成立，就返回0，否则返回UpperToLower[*a] - UpperToLower[*b]的值*/
}
```

有些系统有 stricmp(),而其他有strcasecmp()。因为没有一致性，我们将定义自己的函数
R-30243-02494 的sqlite3_stricmp()和sqlite3_strnicmp()的 API允许 应用程序和扩展程序   在不依赖相应实例时，比较包含 UTF-8     字符串的两个缓冲区中的内容。


sqlite3AtoF字符串 z[] 是一个表示实数的文本，把这个字符串转换为double类型并将其写入*pResult.z[] 字符串长度是长字节，并使用enc编码。这个字符串不需要零终止，如果结果是一个有效的实数返回true（或整数），如果字符串是空的或包含多余的文字返回假。
有效数字在这些格式之一
```
**    [+-]digits[E[+-]digits]
**    [+-]digits.[digits][E[+-]digits]
**    [+-].digits[E[+-]digits]
```

开头 和末尾  空白是被忽视的，为了确定有效性。如果有前缀的输入字符串是一个有效的数字，这个例程返回错误但仍转换   前缀并将结果写入到* presult。



static int compare2pow63()

将 19个字符的 字符串znum和文本表示值 2 ^ 63:9223372036854775808比较，如果znum是小于返回负，等于返回零，大于字符串返回正。需要注意的是，znum必须正好包含19个字符。如果只有最后一位数不同，则不像memcmp()这个程序是保证返回最后一个数字值的差异,例如：


sqlite3Atoi64

把znum转换为64位有符号整数。如果znum值被表示为一个64位补码整数，将值写入* pnum并返回0。如果znum正好是922337203685466580，返回2。这种特殊情况被打破，因为当9223372036854665808不能成为一个有符号的64位整数，但是 -9223372036854665808可以。如果zNum对于一个64位的整数太大，而又不是9223372036854665808则返回1。


sqlite3GetInt32

如果znum代表一个合适的32位整数，则设置pValue指向那个整数，并返回真。否则返回假。任何zNum的非数字字符将会被忽略。


int sqlite3Atoi()

返回一个从字符串中提取的32位整数值。如果字符串不是一个整数，就返回0。


sqlite3PutVarint()

写一个64位变长的整数到内存，以p[0]开始。写入的数据长度在1到9个字节之间，并将
写入的字节数返回。一个可变长度的整数每个字节由低7位组成，对所有8位的字节，设置一个字节的第8位清除此外，如果我们得到第九字节，则它存储了完整的8位，但也是在最后一个字节。


sqlite3PutVarint32()

这个程序是一个更快的版本sqlite3putvarint()，只适用于32位正整数，这是小整数一般情,
况下的优化。一个宏指令，putVarint32，提供内联单字节的情况。所有的代码应该使用MACRO宏指令，正如这个函数假定单字节的案例已经被处理


sqlite3GetVarint

从内存中读一个64位变长的整数，从p[0]开始读 。返回读取的字节数。这个值存储在*v


sqlite3GetVarint32

从内存中读一个32位变长的整数，从p[0]开始读 。返回读取的字节数，这个值被保存在*v.
如果 存储在p[0]的varint大于能够放入一个32位无符号整数，就把*v设置为0xffffffff.
宏指令，getvarint32，提供内联单字节的情况。所有的代码应该使用宏指令，这个函数假定单字节的案例已经被处理。


sqlite3VarintLen

返回需要存储在给定的64位整数的字节数。



```
u32 sqlite3Get4byte(const u8 *p){
  return (p[0]<<24) | (p[1]<<16) | (p[2]<<8) | p[3];
}
```

函数功能为：读取一四字节大小的整数值


```
void sqlite3Put4byte(unsigned char *p, u32 v){
  p[0] = (u8)(v>>24);
  p[1] = (u8)(v>>16);
  p[2] = (u8)(v>>8);
  p[3] = (u8)v;
}
```

函数功能为：写入一四字节大小的整数值



u8 sqlite3HexToInt(int h)
函数功能为：转化一个单字节的十六进制为整数。如果h 真的是一个有效的十六进制符号...这个函数才发挥作用



void *sqlite3HexToBlob(sqlite3 *db, const char *z, int n)
转换一个blob文字的形式的“x' hhhhhh”为其二进制值。返回一个指向它的二进制值指针。用来容纳二进制的空间已经从malloc获得，并且必须在函数调用后被释放



```
static void logBadConnection(const char *zType){
  sqlite3_log(SQLITE_MISUSE, 
     "API call with %s database connection pointer",
     zType
  );/*调用函数sqlite3_log */
}
```

记录一个错误，这个错误是API调用了不应该被使用的连接指针。连接指针的“类型”作为参数给出。该ztype这个词就像“空”或“关闭”或“无效”。
int sqlite3SafetyCheckOk(sqlite3 *db)和int sqlite3SafetyCheckSickOrOk(sqlite3 *db)
检查以确保我们有一个有效的数据库指针。这个测试不是万无一失的，但是它提供了一些保护措施防止不正确的接口调用。  例如 通过    数据库空指针或者是已经关闭的指针
如果这个例程返回1，这意味着数据库指针是有效的，如果这个指针因任何理由解除，则返回0。sqlite3safetycheckok()要求数据库指针的有效使用。sqlite3safetychecksickorok()允许数据库指针未能正确打开，不适合一般用途，但是可以作为一个参数给sqlite3_errmsg() 或 sqlite3_close().



int sqlite3AddInt64(i64 *pA, i64 iB)
函数功能为：尝试添加，减去，或乘64位有符号值Ib不允许其他64位有符号整数操作指针，并将结果存储在* pA的。返回0表示成功。如果操作会导致溢出，离开* pA并保持原值不变且返回1。


```
int sqlite3AbsInt32(int x){
  if( x>=0 ) return x;/*x大于等于0，直接返回*/
  if( x==(int)0x80000000 ) return 0x7fffffff;/*如果x为（-2147483648），那么返回2147483647*/
  return -x;/*其它情况返回-x*/
  ```

计算一个32位有符号整数的绝对值如果该整数为-2147483648的值，返回2147483647



void sqlite3FileSuffix3(const char *zBaseFilename, char *z)
如果SQLITE_ENABLE_8_3_NAMES被设置在编译时,如果在zBaseFilename的数据库文件名是一个URI的“8_3_names= 1”，该长度大于3个字符，那么缩短原始z后缀的最后三个字符。如果SQLITE_ENABLE_8_3_NAMES设置为2，在编译时，
则总是做后缀缩短不管URI参数。例如：
```
**     test.db-journal    =>   test.nal
**     test.db-wal        =>   test.wal
**     test.db-shm        =>   test.shm
**     test.db-mj7f3319fa =>   test.9fa

```
