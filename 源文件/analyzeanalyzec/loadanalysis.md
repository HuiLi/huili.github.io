# loadAnalysis函数

static void loadAnalysis(Parse *pParse, /* 解析器上下文 */

int iDb/*指定分析的数据库*/
)

生成的代码会导致大多数最近分析的索引将被加载到内部hash表，这样比较便于使用。
