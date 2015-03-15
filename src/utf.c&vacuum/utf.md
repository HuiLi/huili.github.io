# 1.头文件—sqliteInt.h；assert.h；vdbeInt.h


## 1.1头文件

```
#include "sqliteInt.h"
#include <assert.h>
#include "vdbeInt.h"
```

1.2分析(1). sqliteInt.h：sqliteInt.h中可规定整型，其中嵌有sqlitelimit.h文件。指明了在表中对于blob和text字节的限制；指明了表、索引、视图、update、groupby、select等的限制，最高能超过32676条记录或字段，SQL语句长度、缓存大小、可以附加数据库的多少、数据库文件的最大页数等等。

(2). assert.h：assert宏的原型定义在<assert.h>中，其作用是如果它的条件返回错误，则终止程序执行.

(3). vdbeInt.h:字节代码在内存中被封装成sqlite3_stmt对象(内部叫做Vdbe，见vdbeInt.h)，Vdbe（或者说statement）包含执行程序所需要的一切。


# 2.头文件—ifndef;endif


## 2.1定义常量

```
#ifndef SQLITE_AMALGAMATION
const int sqlite3one = 1;
#endif
```

## 2.2分析
(1). 在#ifndef中定义了一个常量sqlite3one，定义变量会出现问题（一般不定义在#ifndef中）
 
(2). 头件中的#ifndef，这是一个很关键的东西。比如你有两个C文件，这两个C文件都include了同一个头文件。而编译时，这两个C文件要一同编译成一个可运行文件，
还是把头文件的内容都放在#ifndef和#endif中。不管你的头文件会不会被多个文件引用，你都要加上这个，这样可以防止声明冲突。

(3).另外还能让代码适应多种环境，如常见的 _C_PLUS_PLUS等，也就是让编译器在不同的环境下选择合适的代码


# 3.编码方式—需要读写的UTF-8， UTF-16LE，UTF-16BE字符集


## 3.1函数头—宏定义读写字符集

static const unsigned char sqlite3Utf8Trans1[]

```
#define WRITE_UTF8
#define WRITE_UTF16LE
#define WRITE_UTF16BE
#define READ_UTF16LE
#define READ_UTF16BE
#define READ_UTF8
```

3.2(1). SQLite只能按照几种标准的字符编码保存数据，即使按照GB2312保存了汉字，SQLite还是按照数据库的字符集解析字符编码。这是问题的关键。数据库的编码格式设置后不能够被改变。Sqlite默认编码是utf-8。数据库的字符编码：

```
PRAGMA encoding; （查看当前数据库的字符编码）
PRAGMA-encoding="UTF-8"; 
PRAGMA-encoding="UTF-16"; 
PRAGMA encoding = "UTF-16le"; (LE指Little Endian)
PRAGMA encoding = "UTF-16be"; (BE指Big Endian
）
```
LE特指低位优先，BE特指高位优先。
UTF-16BE: 16 位 UCS 转换格式，Big Endian（最低地址存放高位字节，符合人们的阅读习惯）字节顺序 。 
UTF-16LE: 16 位 UCS 转换格式，Little-endian（最高地址存放高位字节）字节顺序  。
UTF-16: 16 位 UCS 转换格式，字节顺序（是高字节在前还是低字节在前）由流中的前两字节中字节顺序标记来确定 。

在第一种形式中，若主数据库已创建，这条pragma返回主数据库使用得文本编码格式，为 "UTF-8", "UTF-16le" (little-endian UTF-16 encoding)或者"UTF-16be" (big-endian UTF-16 encoding)中的一种。 若主数据库未创建，返回值为当前会话创建的主数据库将要使用的文本编码格式。

第二种及以后几种形式只在主数据库未创建时有效。这时该pragma设置当前会话创建的主数据库将要使用的文本编码格式。 "UTF-16"表示"使用本机字节顺序的UTF-16编码"。若这些形式在主数据库创建后使用，将被忽略且不产生任何效果。

至于UTF16-LE和UTF16-BE，与计算机的CPU构架有关。我们一般的X86系统都是Little Endian的，可以认为UTF16=UTF16-LE.

(2). 如果SQLite将汉字按照GB2312编码保存到数据库中（通过UltraEdit二进制查看，如果能够看到中文，说明是按照GB2312保存的），查询肯定会出问题（不考虑BLOB字段）。使用sqlite3.exe执行插入操作，保存的汉字都是按照GB2312编码保存的，因此使用like匹配时，肯定会出问题。
    为了提高Unicode的编码效率，于是就出现了UTF-8编码。UTF-8可以根据不同的符号自动选择编码的长短。比如英文字母可以只用1个字节就够了。UTF-8的编码是这样得出来的，以”汉”这个字为例：“汉”字的Unicode编码是U+00006C49，然后把U+00006C49通过UTF-8编码器进行编码，最后输出的UTF-8编码是E6B189。

(3). Unicode是Unicode.org制定的编码标准，目前得到了绝大部分操作系统和编程语言支持。Unicode.org官方对Unicode的定义是：Unicode provides a unique number for every character。可见，Unicode所做的是为每个字符定义了一个相应的数字表示。比如，“a“的Unicode值是0x0061,“一”的Unicde值是0x4E00，这是最简单的情况.

Unicode.org定义了百万个以上的字符，如果将所有的字符用统一的格式表示，需要的是4个字节。“a“的Unicode表示就会变成0x00000061，而“一“的Unicode值是0x00004E00.
    
但是，其实绝大部分字符只使用2个字节就可以表示了。英文的Unicode范围是0x0000-0x007F，中文的Unicode范围是0x4E00-0x9F**，真正需要扩展到4个字节来表示的字符少之又少，所以有些系统直接使用2个字节来表示Unicode。比如Windows系统上，Unicode就是两个字节的。对于那些需要4个字节才能表示的字符，使用一种代理的手法来扩展(其实就是在低两个字节上做一个标记，表示这是一个代理，需要连接上随后的两个字节，才能组成一个字符)。这样的好处是大量的节约了存取空间，也提高了处理的速度。这种Unicode表示方法就是UTF16。一般在Windows平台上，提到Unicode,那就是指UTF16了。
```
Utf-8:  
0xxxxxxx 
110xxxxx-10xxxxxx 
1110xxxx-10xxxxxx-10xxxxxx 
11110xxx-10xxxxxx-10xxxxxx-10xxxxxx 
111110xx-10xxxxxx-10xxxxxx-10xxxxxx-10xxxxxx 
1111110x-10xxxxxx-10xxxxxx-10xxxxxx-10xxxxxx-10xxxxxx 
```

由于UTF8可以方便的转换为UTF16和UTF32（不需要码表，转换算法可以在Unicode.org上找到C代码）。而且UTF8在每个操作系统平台上的实现都是一样的，也不存在跨平台的问题，所以UTF8成为跨平台的Unicode很好的解决方案。当然，对于中文来说，由于每个字符需要3个字节才能表示，还是有点浪费的。
## 3.2函数工作流程

### (1). static const unsigned char sqlite3Utf8Trans1[]

创建表然后添加数据，使用代码即可读出中文， 后边的函数包括UTF-8转化后的功能函数。这个查找表把sqlite3当中的Utf8翻译过来，并列出所有的具体编码，此查找表用于帮助解码多字节UTF8 编码值的第一个字节。
### (2). #define WRITE_UTF8

   ①.c作为自变量，zOut作为因变量，并把自变量c划分成不同的区间，此时是以UTF8的编码往内存中写入。
   
   ②.当c<0x00080时，在UTF-8编码下，把c以值0xFF赋予zOut,zOut并自增。
   
   ③.当c<0x00800时，在UTF-8编码下，把c以值0x1F(此时c>>6)并加上0xC0赋予指针zOut,zOut并自增。同理，把c以值0x3F并加上0x80赋予指针zOut,zOut并自增。
   
   ④.当c<0x10000时，在UTF-8编码下，把c以值0x0F( 此时c>>12) 并加上0xE0赋予指针zOut,zOut并自增。同理，把c以值0x3F( 此时c>>12) 并加上0x80赋予指针zOut,zOut并自增。此时，把c也以值0x3F( 此时不限制c) 并加上0x80赋予指针zOut,zOut并自增。
   
   ⑤. 当c>0x10000时,在UTF-8编码下，把c以值0x07( 此时c>>18) 并加上0xC0赋予指针zOut,zOut并自增。同理，把c以值0x3F( 此时c>>6) 并加上0x80赋予指针zOut,zOut并自增。在UTF-8编码下，把c以值0x3F( 此时c>>6) 并加上0x80赋予指针zOut,zOut并自增。同理，把c以值0x3F( 此时不限制c) 并加上0x80赋予指针zOut,zOut并自增。
### (3). #define WRITE_UTF16LE

   ①.c作为自变量，zOut作为因变量，并把自变量c划分成不同的区间，此时是往内存中写入。进行编码时， UTF-16LE charset不会将字节顺序标记写入内存时即它们所编出的码每个字符只占二个字节，要注意的是解码时要使用同样的编码方式，不然会出现问题乱码 。
   使用 utf-16LE 对低字节序进行解码，忽略字节顺序标记，即不会将流前二字节内。因为c用低字节表示，所以前面需要加上 FF，FE字节顺序标记来指示它。
   
   ②.当c<0xFFFF时，在UTF-8编码下，把c以值0x00FF赋予指针zOut,zOut并自增。同理，把c以值0x00FF( 此时c>>8)赋予指针zOut,zOut并自增。
   
   ③.当c>0xFFFF时,分四步，在UTF-8编码下，把c以值0x003F( 此时c>>10)并加上c减去值0x10000( 此时(c-0x10000)>>10)并以值0x00C0赋予指针zOut,zOut并自增。同理，后三部类似。
   
### (4).#define WRITE_UTF16BE

   ①.c作为自变量，zOut作为因变量，并把自变量c划分成不同的区间，此时是往内存中写入。进行编码时， UTF-16BE charset 不会将字节顺序标记写入内存时即它们所编出的码每个字符只占二个字节，要注意的是解码时要使用同样的编码方式，不然会出现问题乱码 。使用 utf-16BE 对高字节序进行解码，忽略字节顺序标记，即不会将流前二字节内。因为c用高字节表示，所以前面需要加上 FF，FE 字节顺序标记来指示它。
   
   ②.当c<0xFFFF时，在UTF-8编码下，把c以值0x00FF(此时c>>8)赋予指针zOut,zOut并自增。同理，把c以值0x00FF赋予指针zOut,zOut并自增。
   
   ③.当c>0xFFFF时,分三步，在UTF-8编码下，把c以值0x00DB并加上c减去值0x10000( 此时(c-0x10000)>>18) 并以值0x03赋予指针zOut,zOut并自增。同理，后两部类似。
(5).#define READ_UTF16LE

①.zIn作为自变量，c作为因变量，TERM作为中间变量，往内存读入。

②.zIn作为指针,自增后赋予c;zIn指针先自增，加1后赋予c。

③.当
```
c>=0xD800 && c<0xE000 && TERM
```
时，情况类似。
(6).#define READ_UTF16BE

   ①.zIn作为自变量，c作为因变量，TERM作为中间变量，往内存读入。情况与READ_UTF16LE类似。
 
 4.记录无效的编码—UTF-8， UTF-16LE，UTF-16BE的情况



## 4.1函数头—无效的编码

```
#define READ_UTF8
u32 sqlite3Utf8Read
int sqlite3VdbeMemTranslate
int sqlite3VdbeMemHandleBom

```



## 4.2

(1)
```
. #define READ_UTF8

```

①.zIn自增后，其指针指向c。

②.当c>=0xc0时sqlite3按照Utf8翻译，并把c-0xc0赋予c。

③.后续步骤类似。

(2). u32 sqlite3Utf8Read
```
①.const unsigned char *zIn,  /* UTF-8字符的第一个字节*/
   const unsigned char **pzNext  /*写第一个字节过去utf - 8字符*/
```

②. 以上和READ_UTF8（）一样但是没有zTerm参数。
对这个例程,我们假设UTF8字符串的值均为zero-terminated。
③.如果TRANSLATE_TRACE被定义,那么每个Mem的值
都被输出到stderr sqlite3VdbeMemTranslate()函数上面。④.这个例程可以通过pMem转换内部文本编码到desiredEnc上。如果已经得到编码,或者如果* pMem不包含一个字符串值，那就说明这是一个错误的字符串。
(3)
```
.int sqlite3VdbeMemTranslate
①. int len;            /* 输出字符串的字节的最大长度*/
  unsigned char *zOut;        /* 输出缓冲区r */
  unsigned char *zIn;          /* I输入缓冲区 */
  unsigned char *zTerm;       /* 输入的结果 */
  unsigned char *z;            /* 输出迭代*/
```

②.用assert()插入pMen的值。

③.定义TRANSLATE_TRACE，SQLITE_DEBUG后，打印输出zBuf。

④如果翻译的结果是用utf-16编码的最小和最大尾数之间的数,那么就需要交换字节顺序。这种情况下处理不同于其它的情况。
   
⑤. 在输出缓冲区len设置为所需的最大字节数。当转换utf - 8编码到utf - 16编码时，会引起最大的长度变化。当一个utf - 8字符转换为2字节utf - 16字符时， nul-terminator需要两个字节的输出缓冲区。

⑥. 在输入缓冲区的开始设置zIn为一点和在结束时，zTerm为 1个字节的字符。在输出缓冲区变量zOut设置为一点,而且从sqlite3_malloc()中可得到内存空间。

(4). int sqlite3VdbeMemHandleBom
①.当字符存储在* pMem 中且在utf - 16字符串的开始时，这个例程允许检查字节顺序。如果有一个已经存在,那么另外的可以被删除，并调整Mem的编码。这个例程不做任何字节交换,它只是被设置Mem.enc。分配(静态、动态空间)的Mem的编码可能会被这个函数改变。


# 5.返回值—UTF-8的返回情况


## 5.1函数头—输出返回值

int sqlite3Utf8CharLen
int sqlite3Utf8To8
### 5.2(1).int sqlite3Utf8CharLen


①.pZ是unicode utf - 8编码的字符串。如果nByte小于零,那么在pZ上，返回unicode字符(但不包括)为第一个0x00字节。如果nByte不小于零，那么返回的编码字符在 pZ的第一个nByte(上或第一个0 x00,无论哪一个先开始)。

②.这个测试函数并不是目前使用的自动化测试套件。因此只有在调试版可用。

### (2).int sqlite3Utf8To8

①.这样做的效果是确保字符串的格式是正确的 utf - 8。错编字符被删除。如果输出超过输入，那么翻译就地完成和抛弃。


# 6.字符编码的转化— utf-16 utf-8 之间的转化 


## 6.1函数头—转化功能

```
char *sqlite3Utf16to8
char *sqlite3Utf8to16
int sqlite3Utf16ByteLen
void sqlite3UtfSelfTest
```

## 6.2
### (1). char *sqlite3Utf16to8


①.把原始编码utf - 16字符串转换成utf - 8编码的字符串。utf - 8字符串的内存从sqlite3_malloc被得到 和必须由调用函数释放。如果有配置错误则返回NULL。
### (2).char *sqlite3Utf8to16


①.通过参数 enc，一个utf - 8编码的字符串可转换为utf -16编码。新的字符串返回一个指针,并* pnOut的值通过字节设定为返回的字符串的长度。不再需要时，则调用sqlite3DbFree()返回的指针。如果malloc出现错误,则返回NULL或db.mallocFailed设标记集。
②.不再需要时，则调用sqlite3DbFree()返回的指针。如果malloc出现错误,则返回NULL或db.mallocFailed设标记集。
### (3).int sqlite3Utf16ByteLen
①.zIn是一个utf - 16编码的unicode字符串，长度至少nChar。
②.pZ返回nChar unicode字符的第一个字节。nChar必须是非负数。
### (4).void sqlite3UtfSelfTest
①.TCL测试函数“translate_selftest”可调用这个例程。
②.编码是对方的逆反编码，原语用在序列化和反序列化编码中。
