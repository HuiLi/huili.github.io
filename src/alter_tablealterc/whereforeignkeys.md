# 生成用来选择有外键约束的全部表的表达式文本
5.1函数头

static char *whereForeignKeys(
Parse *pParse /*解析上下文*/
, Table *pTab   /*被打开的表*/
)

5.2 工作流程

（1）循环判断外键约束指针p是不是对应打开的表有外键约束，保持p->pNextTo,

（2）调用whereOrName函数，返回可以选择外键约束的表达式zWhere
