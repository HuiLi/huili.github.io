# 宏定义
转换类型分为不同的类别，正如以下列举所定义的：

    #define etRADIX       1 /*整数类型。%d, %x 和 %o 等等*/
    #define etFLOAT       2 /*浮点型*/
    #define etEXP         3 /*指数表示。%e 和 %E*/
    #define etGENERIC     4 /*根据指数，确定是浮点数还是指数表示。%g*/
    #define etSIZE         5 /*到目前为止处理过的字符的返回数。%n*/
    #define etSTRING      6 /*字符串。%s*/
    #define etDYNSTRING   7 /*动态分配的字符串。%z*/
    #define etPERCENT     8 /*百分比符号。%%*/
    #define etCHARX       9 /*字符。%c
其余的都是扩展，而不是通常的printf发现：

    #define etSQLESCAPE    10
    #define etSQLESCAPE2   11
    #define etTOKEN        12    指向一个令牌结构的指针
    #define etSRCLIST       13    指向SrcList的指针
    #define etPOINTER      14    %p 的转换
    #define etSQLESCAPE3   15    %w -> Strings with '\"' doubled
    #define etORDINAL      16    %r -> 1st, 2nd, 3rd, 4th, etc.  仅限英语
    #define etINVALID       0     任何不能识别转换的

下面定义的是各种标识，et_info.flags允许的值如下：

    #define FLAG_SIGNED  1     /*如果要转换的数是有符号数就为真*/
    #define FLAG_INTERN  2     /*如果只作内部使用就为真*/
    #define FLAG_STRING  4     /*允许无限精度*/

下面定义的SQLITE_PRINT_BUF_SIZE是输出缓冲区的大小，需要注意的是：在一个小容量栈的机器指令中，如果需要的话，你可以重新定义使它变得更小：

    #ifndef SQLITE_PRINT_BUF_SIZE
    # define SQLITE_PRINT_BUF_SIZE 70
    #endif
    #define etBUFSIZE SQLITE_PRINT_BUF_SIZE
