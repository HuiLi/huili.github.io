# ID序列的创建
IdList *sqlite3IdListAppend(sqlite3 *db, IdList *pList, Token *pToken)在给定的IdList添加一个新元素,如果需要的话建立一个新的IdList。
sqlite3IdListIndex(IdList *pList, const char *zName)返回名为zId的标示符中的pList的索引，其功能类似于查找IdList。
