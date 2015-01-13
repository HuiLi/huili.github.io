# sqlite3GenerateRowIndexDelete
<pre>
void sqlite3GenerateRowIndexDelete(
  Parse *pParse,     /* Parsing and code generating context */
  Table *pTab,       /* Table containing the row to be deleted */
  int iCur,          /* Cursor number for the table */
  int *aRegIdx       /* Only delete if aRegIdx!=0 && aRegIdx[i]>0 */
)
</pre>
函数功能：此过程生成VDBE代码导致与单个表的单个行相关联的所有的索引条目被删除。当这个例程被调用的时候VDBE 必须处于特定的状态。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.读/写指针指向pTab，这个表包含要删除的行，该表必须通过游标号码iCur 打开。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.pTab所有索引的读写游标必须用游标号码iCur+i (i 表示第几个索引)打开。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.iCur 游标必须指向要删除的行。
