# sqlite3MaterializeView
<pre>
void sqlite3MaterializeView(
  Parse *pParse,       /* Parsing context */
  Table *pView,        /* View definition */
  Expr *pWhere,        /* Optional WHERE clause to be added */
  int iCur             /* Cursor number for ephemerial table */
){
  SelectDest dest;
  Select *pDup;
  sqlite3 *db = pParse->db;
  pDup = sqlite3SelectDup(db, pView->pSelect, 0);
  if( pWhere ){
    SrcList *pFrom;
    pWhere = sqlite3ExprDup(db, pWhere, 0);
    pFrom = sqlite3SrcListAppend(db, 0, 0, 0);
    if( pFrom ){
      assert( pFrom->nSrc==1 );
      pFrom->a[0].zAlias = sqlite3DbStrDup(db, pView->zName);
      pFrom->a[0].pSelect = pDup;
      assert( pFrom->a[0].pOn==0 );
      assert( pFrom->a[0].pUsing==0 );
    }else{
      sqlite3SelectDelete(db, pDup);
    }
    pDup = sqlite3SelectNew(pParse, 0, pFrom, pWhere, 0, 0, 0, 0, 0, 0);
  }
  sqlite3SelectDestInit(&dest, SRT_EphemTab, iCur);
  sqlite3Select(pParse, pDup, &dest);
  sqlite3SelectDelete(db, pDup);
}
</pre>
函数的结果是把视图存放在一个临时的表空间中，之后用Expr *sqlite3LimitWhere函数产生一个表达树来实现DELETE and UPDATE语句中的WHERE, ORDER BY, LIMIT/OFFSET 部分

调用函数 sqlite3SelectDup()、sqlite3ExprDup()、sqlite3SrcListAppend()、sqlite3DbStrDup()、sqlite3SelectDelete()、sqlite3SelectNew()、sqlite3SelectDestInit()、sqlite3Select()、sqlite3SelectDelete()
