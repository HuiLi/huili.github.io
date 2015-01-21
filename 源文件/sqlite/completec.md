# complete.c模块源码分析
<br></br>
主要功能：
<br></br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此文件包含C代码，用于实现sqlite3_complete()的API。本代码用的是tokenizer.c源文件的一部分。但是分离出来了，该代码会自动省略静态链表而不使用它。<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要代码功能分析:<br></br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这段程序是相似于sqlite3_complete（）例程，所不同的是该参数需要是UTF-16进行编码，而不是UTF-8。<br></br>



    int sqlite3_complete16(const void *zSql){
      sqlite3_value *pVal;
      char const *zSql8;
      int rc = SQLITE_NOMEM;

    #ifndef SQLITE_OMIT_AUTOINIT
      rc = sqlite3_initialize();
      if( rc ) return rc;
    #endif
      pVal = sqlite3ValueNew(0);
      sqlite3ValueSetStr(pVal, -1, zSql, SQLITE_UTF16NATIVE, SQLITE_STATIC);
      zSql8 = sqlite3ValueText(pVal, SQLITE_UTF8);
      if( zSql8 ){
        rc = sqlite3_complete(zSql8);
      }else{
        rc = SQLITE_NOMEM;
      }
      sqlite3ValueFree(pVal);
      return sqlite3ApiExit(0, rc);
    }
    #endif /* SQLITE_OMIT_UTF16 */
    #endif /* SQLITE_OMIT_COMPLETE */
