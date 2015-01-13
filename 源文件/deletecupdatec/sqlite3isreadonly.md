# sqlite3IsReadOnly
<pre>
int sqlite3IsReadOnly(Parse *pParse, Table *pTab, int viewOk){
if( ( IsVirtual(pTab)
&& sqlite3GetVTable(pParse->db, pTab)->pMod->pModule->xUpdate==0 )
|| ( (pTab->tabFlags & TF_Readonly)!=0
&& (pParse->db->flags & SQLITE_WriteSchema)==0
&& pParse->nested==0 )
){
 sqlite3ErrorMsg(pParse, "table %s may not be modified", pTab->zName);
 return 1;
}
</pre>
函数目的是检查以确保给定的表是可写的。如果它不是可写，生成一条错误消息，并返回1。如果它是可写返回0。

调用了sqlite3ErrorMsg（）函数
