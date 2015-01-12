# date.c模块源码分析（6）--（7）

（6）currentTimeFunc函数这个函数使用C-library中time()，gmtime()和strftime()函数。这个格式字符串被作为用户数据提供给strftime()函数。
（7）sqlite3RegisterDateTimeFunctions函数注册所有上述C函数作为SQL的函数。
    void sqlite3RegisterDateTimeFunctions(void){
      /*定义aDateTimeFuncs数组*/
      static SQLITE_WSD FuncDef aDateTimeFuncs[] = {
        /*先测试SQLITE_OMIT_DATETIME_FUNCS是否被宏定义过，
        **如果没有被定义了就执行下面的语句，否则就执行else下面的语句。
        */
    #ifndef SQLITE_OMIT_DATETIME_FUNCS
        FUNCTION(julianday,        -1, 0, 0, juliandayFunc ),/*用于创建一个标量函数的函数定义julianday由C函数juliandayFunc实现。*/
        FUNCTION(date,             -1, 0, 0, dateFunc      ),
        FUNCTION(time,             -1, 0, 0, timeFunc      ),
        FUNCTION(datetime,         -1, 0, 0, datetimeFunc  ),
        FUNCTION(strftime,         -1, 0, 0, strftimeFunc  ),
        FUNCTION(current_time,      0, 0, 0, ctimeFunc     ),
        FUNCTION(current_timestamp, 0, 0, 0, ctimestampFunc),
        FUNCTION(current_date,      0, 0, 0, cdateFunc     ),
    #else /*如果SQLITE_OMIT_DATETIME_FUNCS被宏定义过，执行下面语句*/
        STR_FUNCTION(current_time,      0, "%H:%M:%S",          0, currentTimeFunc),
        STR_FUNCTION(current_date,      0, "%Y-%m-%d",          0, currentTimeFunc),
        STR_FUNCTION(current_timestamp, 0, "%Y-%m-%d %H:%M:%S", 0, currentTimeFunc),
    #endif
      };
      int i;
      FuncDefHash *pHash = &GLOBAL(FuncDefHash, sqlite3GlobalFunctions); /*定义一个函数定义的哈希表*pHash*/
      FuncDef *aFunc = (FuncDef*)&GLOBAL(FuncDef, aDateTimeFuncs); /*定义一个指向FuncDef结构体的指针*aFunc*/
       /*遍历aDateTimeFuncs数组。把Func[i]插入到pHash哈希表*/
      for(i=0; i<ArraySize(aDateTimeFuncs); i++){
        sqlite3FuncDefInsert(pHash, &aFunc[i]); /*把Func[i]插入到pHash哈希表*/
      }

