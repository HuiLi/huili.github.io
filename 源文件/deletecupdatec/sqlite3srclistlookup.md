# sqlite3SrcListLookup
<pre>
Table *sqlite3SrcListLookup(Parse *pParse, SrcList *pSrc){
  struct SrcList_item *pItem = pSrc->a;
  Table *pTab;
  assert( pItem && pSrc->nSrc==1 );
  pTab = sqlite3LocateTable(pParse, 0, pItem->zName, pItem->zDatabase);
  sqlite3DeleteTable(pParse->db, pItem->pTab);
  pItem->pTab = pTab;
  if( pTab ){
    pTab->nRef++;
  }
  if( sqlite3IndexedByLookup(pParse, pItem) ){
    pTab = 0;
  }
  return pTab;
}
</pre>

函数的作用查找所有名字为pSrc的表，如果没有找到任何表，添加一个错误消息pParse - > zErrMsg并返回NULL。如果所有的表都找到，返回一个指针，指向最后一个表。
