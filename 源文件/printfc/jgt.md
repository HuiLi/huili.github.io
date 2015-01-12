# 结构体
每个装入的转换字符(例:“% d”中的“d”)由以下结构的实例描述：

    typedef struct et_info {   /*关于每一个格式化区域的信息*/
        char fmttype;            /*格式化区域的代码字母*/
        etByte base;             /*基数转换的基础*/
        etByte flags;            /*一个或多个FLAG_下面的常量*/
        etByte type;            /*转换模式*/
        etByte charset;          /*aDigits[]中偏移量的数字字符串*/
        etByte prefix;           /*aPrefix[]中偏移量的前缀字符串*/
    } et_info;
