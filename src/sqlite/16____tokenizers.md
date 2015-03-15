# 自定义(用户)实现tokenizers
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;以及内置"simple (可能受)"icu ", "porter "和"tokenizers , FTS 导出一个接口, 允许用户实现定制tokenizers 使用c用来创建一个新的tokenizer 接口是在fts3_tokenizer 定义和描述h源文件注册新的FTS tokenizer 类似于注册新的虚拟表的模块, 带SQLite用户将指针传递到包含一个结构的指针到各种构成新实现的回调函数tokenizer 类型为tokenizers , 该结构(定义在fts3_tokenizerh )方法"sqlite3_tokenizer_module "
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FTS 不公开的c-function 该用户拨打新的tokenizer 数据库句柄的类型相反, 必须作为SQL 指针编码二进制大对象blob 值, 然后传递要通过"SQL FTS 引擎进行特殊标量函数, "fts3_tokenizer() "调用fts3_tokenizer( )函数可能与一个或两个参数, 如下所示:

    SELECT fts3_tokenizer( < tokenizer-name > );
    SELECT fts3_tokenizer( < tokenizer-name > , < sqlite3_tokenizer_module ptr > );

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其中是一个字符串, 用于tokenizer 和存储在一个sqlite3_tokenizer_module 结构的指针作为SQL 编码二进制大对象blob如果第二个参数为正的有限奇数整数, 它被注册为tokenizer , 并将返回的如果只选择了一个参数, 传递给指针添加到当前链接注册为tokenizer 实现是作为二进制大对象blob 返回, 编码或者, 如果没有这样的tokenizer 是否存在, 将引发一个SQL 异常(错误)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

    以下示例块包含从c代码中调用fts3_tokenizer( )函数:
    /*
    ** 注册一个tokenizer 实现与FTS3 或FTS4.
    */
    int registertokenizer(
    sqlite3 *db ,
    char *zname ,
    const sqlite3_tokenizer_module *p
    ){
    int rc ;
    sqlite3_stmt *pstmt ;
    const char *zsql ="SELECT fts3_tokenizer(?  , ? ) ";
    rc =sqlite3_prepare_v2(db , zsql , -1, & pstmt , 0) ;
    if( rc!=SQLITE_OK ){
    返回rc ;
    }
    sqlite3_bind_text(pstmt , 1, zname , -1,
    SQLITE_STATIC);
    sqlite3_bind_blob(pstmt , 2, & p, sizeof(p) ,
    SQLITE_STATIC);
    sqlite3_step(pstmt) ;
    返回sqlite3_finalize(pstmt) ;
    }
    /*
    ** 查询名为zname 为tokenizer FTS 实现的
    */
    int querytokenizer(
    sqlite3 *db ,
    char *zname ,
    const sqlite3_tokenizer_module **pp
    ){
    int rc ;
    sqlite3_stmt *pstmt ;
    const char *zsql ="SELECT fts3_tokenizer(? ) ";
    *pp =0;
    rc =sqlite3_prepare_v2(db , zsql , -1, & pstmt , 0) ;
    if( rc!=SQLITE_OK ){
    返回rc ;
    }
    sqlite3_bind_text(pstmt , 1, zname , -1,
    SQLITE_STATIC);
    if( SQLITE_ROW==sqlite3_step(pStmt) ){
    if( sqlite3_column_type(pstmt , 0)==SQLITE_BLOB ){
    memcpy(pp , sqlite3_column_blob(pstmt 、0) sizeof(*pp))     ;
    }
    }
    返回sqlite3_finalize(pstmt) ;
    }

