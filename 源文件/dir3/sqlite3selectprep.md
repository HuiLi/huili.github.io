# sqlite3SelectPrep函数
对select语句中的子句进行处理，如：为from后连接表，建立临时空间，存储中间结果。将on子句和using子句连接表的条件，转化为where子句中，进行判断，得到布尔结果。
<pre>
void sqlite3SelectPrep(
	  Parse *pParse,         /*解析上下文*/
	  Select *p,            /*编写SELECT语句*/
	  NameContext *pOuterNC /*针对容器的命名上下文*/
	){
	  sqlite3 *db;/*声明一个数据库连接*/
	  if( NEVER(p==0) ) return;/*如果SELECT为空，直接返回*/
	  db = pParse->db;/*将语法解析树中的数据库赋值给db*/
	  if( p->selFlags & SF_HasTypeInfo )
	     return;/*如果SELECT结构体中的selFlags为SF_HasTypeInfo，直接返回。因为已含类型信息*/
	  sqlite3SelectExpand(pParse, p);/*查找SELECT中的扩展符号*/
	  if( pParse->nErr || db->mallocFailed )
	     return;/*如果解析树中有错误或者分配内存出错也返回*/
	  sqlite3ResolveSelectNames(pParse, p, pOuterNC);/*解析SELECT中命名上下文*/
	  if( pParse->nErr || db->mallocFailed )
	     return;/*如果解析后解析树中有错误或者分配内存出错也返回*/
	  sqlite3SelectAddTypeInfo(pParse, p);/*添加SELECT类型信息到语法解析树中*/
	}</pre>
