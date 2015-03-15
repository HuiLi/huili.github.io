# Tokenizer运行原理
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当执行一个包含SQL语句的字符串时，接口程序要把这个字符串传递给tokenizer。Tokenizer的任务是把原有字符串分成一个个标示符，并把这些标示符传递给剖析器。Tokenizer是在C文件夹tokenize.c中用手编译的。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在这个设计中需要注意的一点是，tokenizer调用parser。熟悉YACC和BISON的人们也许会习惯于用parser调用 tokenizer。 The author of SQLite的作者已经尝试了这两种方法，并发现用tokenizer调用parser会使程序运行的更顺利。YACC使程序更滞后一些。
