# Printf.c中数据类型的定义
<pre>
#define etRADIX       1  整数类型.  %d, %x, %o, and so forth
#define etFLOAT       2  浮点数.  %f
#define etEXP         3  指数表示. %e and %E
#define etGENERIC     4  根据指数，确定是浮点数还是指数表示. %g
#define etSIZE         5  到目前为止处理过的字符的返回数. %n
#define etSTRING      6  字符串. %s
#define etDYNSTRING   7  动态分配的字符串. %z
#define etPERCENT     8  百分比符号. %%
#define etCHARX       9  字符. %c

其余的都是扩展，而不是通常的printf
#define etSQLESCAPE    10
#define etSQLESCAPE2   11
#define etTOKEN        12    指向一个令牌结构的指针
#define etSRCLIST       13    指向SrcList的指针
#define etPOINTER      14    %p 的转换
#define etSQLESCAPE3   15    %w -> Strings with '\"' doubled
#define etORDINAL      16    %r -> 1st, 2nd, 3rd, 4th, etc.  仅限英语
#define etINVALID       0     任何不能识别转换的
typedef unsigned char etByte;   “etbyte”是一个无符号的8位值
每个内置转换字符（例如：“d”在“%d”）是由下述结构的实例描述

定义一个结构体
typedef struct et_info {      关于每一个格式化区域的信息
  char fmttype;            格式化区域的信息
  etByte base;             基数转换的基址
  etByte flags;             的一个或多个FLAG_常数以下的
  etByte type;             范式转换
  etByte charset;           aDigits[]中偏移量的数字字符串
  etByte prefix;            aPrefix[]中偏移量的前缀字符串
} et_info;


et_info.flags的值
#define FLAG_SIGNED  1      如果转变的值转变为有符号的值
#define FLAG_INTERN  2      如果只供内部使用
#define FLAG_STRING  4      允许无限精度

下表是线性查找，因此好的把最常用的转换类型。
static const char aDigits[] = "0123456789ABCDEF0123456789abcdef";
static const char aPrefix[] = "-x0\000X0";
static const et_info fmtinfo[] = {
  {  'd', 10, 1, etRADIX,      0,  0 },{  's',  0, 4, etSTRING,     0,  0 },
{  'g',  0, 1, etGENERIC,    30, 0 },{  'z',  0, 4, etDYNSTRING,  0,  0 },
  {  'q',  0, 4, etSQLESCAPE,  0,  0 },{  'Q',  0, 4, etSQLESCAPE2, 0,  0 },
  {  'w',  0, 4, etSQLESCAPE3, 0,  0 }, {  'c',  0, 0, etCHARX,      0,  0 },
  {  'o',  8, 0, etRADIX,      0,  2 },{  'u', 10, 0, etRADIX,      0,  0 },
  {  'x', 16, 0, etRADIX,      16, 1 },{  'X', 16, 0, etRADIX,      0,  4 },
#ifndef SQLITE_OMIT_FLOATING_POINT
  {  'f',  0, 1, etFLOAT,      0,  0 },{  'e',  0, 1, etEXP,        30, 0 },
  {  'E',  0, 1, etEXP,        14, 0 },{  'G',  0, 1, etGENERIC,    14, 0 },
#endif
  {  'i', 10, 1, etRADIX,      0,  0 },{  'n',  0, 0, etSIZE,       0,  0 },
  {  '%',  0, 0, etPERCENT,    0,  0 },{  'p', 16, 0, etPOINTER,    0,  1 },
其余的设置为flag_intern，因此仅供内部使用
{  'T',  0, 2, etTOKEN,      0,  0 }, {  'S',  0, 2, etSRCLIST,    0,  0 },
  {  'r', 10, 3, etORDINAL,    0,  0 },
};
如果定义sqlite_omit_floating_point，浮点数转换将不工作。
et_getdigit
val 是一个double类型的4，例如0.1<=*val<10.0，返回一个数的最前的数字的ascii码，然后再把*val乘以10之后，重新正常化表示出来。
例如：输入时*val = 3.14159，输出为*val = 1.4159
cnt是一个计数器用来每次进行增加的，如果超过16（这个是64浮点数中的有效位数）之后，就返回0。
这个函数用于得到数字的有效数字。
</pre>
