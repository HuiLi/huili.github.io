# et_getdigit

    static char et_getdigit(LONGDOUBLE_TYPE *val, int *cnt)
    “*val”是double类型的数，例如0.1<=*val<10.0，返回一个数的最前的数字的a    scii码，然后再把*val乘以10之后重新正常化。

例如：

    输入：     *val = 3.14159
    输出：     *val = 1.4159    function return = '3'

    计数器*cnt每次都会增加，如果超过16（64位浮点数中的有效位数），就返回0。对应的语句为
    if( (*cnt)<=0 ) return ‘0’;
