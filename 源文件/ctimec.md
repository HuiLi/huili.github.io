# Ctime.c模块源码分析
3、Ctime.c分析

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;简介：   这个文件反应了用于报告哪一个编译时间选项与SQLite共同建立时的历程。该数组看起来很大，但是在一点典型的安装实际使用只有很少的编译时间选项被使用，所以大多数的时候这个数据通常是相当短并且使用的内存空间很小。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本文件在SQLITE_OMIT_COMPILEOPTION_DIAGS没有被预定义的情况下执行。
（1）static const char * const azCompileOpt[] 定义编译时间选项数组。
（2）sqlite3_compileoption_used（）函数返回1或0表示给出的编译时间选项名称是否被定义在编译时。

        int sqlite3_compileoption_used(const char *zOptName) {
      int i, n;
      /*sqlite3StrNICmp()是比较函数，如果zOptName的前缀是SQLITE_返回0.
      **如果zOptName是以"SQLITE_"开头，则zOptName指向zOptName+7，即忽略"SQLITE_"开头。
      */
      if( sqlite3StrNICmp(zOptName, "SQLITE_", 7)==0 ) zOptName += 7;
      n = sqlite3Strlen30(zOptName);/*sqlite3Strlen30（）函数计算字符串zOptName的长度*/

      /* Since ArraySize(azCompileOpt) is normally in single digits, a
      ** linear search is adequate.  No need for a binary search.
      **因为ArraySize(azCompileOpt)的大小通常是个位数字的，线性搜索就能满足，不需要二分查找。
      */

      for(i=0; i<ArraySize(azCompileOpt); i++){ /*ArraySize返回azCompileOpt数组元素的数量*/
      /*判断zOptName是否等于azCompileOpt[i]。相等即该编译时间选择名称被使用则返回1.否者返回0*/
        if(   (sqlite3StrNICmp(zOptName, azCompileOpt[i], n)==0)
           && ( (azCompileOpt[i][n]==0) || (azCompileOpt[i][n]=='=') ) ) return 1;
      }
      return 0;
    }




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;（3）sqlite3_compileoption_get（）返回第N个编译时的选项字符串。如果N超出范围，则返回一个NULL指针。

    const char *sqlite3_compileoption_get(int N){
    /*ArraySize返回azCompileOpt数组元素的数量*/
      if( N>=0 && N<ArraySize(azCompileOpt) ){/*N大于等于0并且N小于数组长度，返回第N个编译时间选择字符串*/
        return azCompileOpt[N];
      }
      return 0;//返回NULL指针
    }
