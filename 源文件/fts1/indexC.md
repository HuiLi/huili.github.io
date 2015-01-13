# 索引的创建

## 6.1创建步骤

第一步：指定索引文档（Document）
        为了说明方便，这里举两个文件（Document）为例：
        Document1：Tom it's too late, you must be go to back.
        Document2：Mommy, I still vigorous. Please, just give me half and two hours.  
        
第二步：将Document1和Document2传给分词组件（Tokenizer） 
分词组件接收到Document1和Document2的文件内容之后，首先，将文档内容按空格划分出所有的单词，停用词和标点符号会在接下来的操作中被去掉。所有的停用词，就是那些对搜索来说没有特别意义，而且频繁出现在文档中的词，例如英文单词中的'"the","a","this"等，汉语中的“的”，“地”，“之后”，“然后”等。在设计不同语言的分词器时，都有需要定义该语言的停用词集合。经过分词以后的结果被称为词元(Token)。

分词组件接收到Document1和Document2的文件内容之后。首先，将文档内容按空格划分出所有的单词，标点符号会在接下来的操作中被去掉。经过分词以后变为词元(Token)。

词元（Token）有如下几种类别 
```
#define TOKEN_EOF         	0    /* End of file 文件终点*/
#define TOKEN_SPACE       	1    /* Any kind of whitespace 空格*/
#define TOKEN_ID          	2    /* An identifier 标识符*/
#define TOKEN_STRING      	3    /* A string literal 字符串*/
#define TOKEN_PUNCT       	4    /* A single punctuation character 控制字符*/
```

Token的定义如下：
```
typedef struct Token {
  const char *z;       /* 指向令牌文本的指针，不能使用'\000'作为终结 */
  short int n;         /* 令牌文本的长度 */
} Token;
```

函数tokenizeString将一个字符串，分割为几个token，并在其中调用getToken函数，确定每一个token的类型。
firstToken函数在字符串中找到第一个字母数字token，并且调用dequoteString函数将字符串中的引用符号删去。 
例如：
	```
		“abc”	→	abc
			‘xyz’	→ 	xyz
		   	[pqr]  	→ 	pqr
		  	`mno` 	→ 	mno
	```

处理后只剩下字母数字token。最终Document1和Document2经过分词以后得到的词元为： 
"Tom","it's","too","late","you","must","be","go","back“
"Mommy","I","still","vigorous","just","give","me","half","tow","hours"。
第三步：为token创建一个索引，索引中记录了每一个项（Term）所属的文档。索引程序为Document1和Document2创建一个字典。如下所示。

在这个倒排链表中，可以获知如下几个定义：

（1）Document Frequency定义包含词的文档数。

（2）Frequency词频率，用来说明文件中包含了几个这样的词。

这样就可以根据单词来搜索上述的这个倒排表二快速的查找到包含所有查找字符串的文件了。

