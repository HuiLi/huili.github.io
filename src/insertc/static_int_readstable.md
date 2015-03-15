# static int readsTable()
输入参数：Parse *p, int iStartAddr, int iDb, Table *pTab
返回值：static int
函数作用：申明一个静态函数仅限于表文件调用。
解释：
输入参数依次是解析器指针p,起始地址，数据库连接实例，表指针pTab；主要是在以后的数据库字典中查找进行插入的表是否存在。
