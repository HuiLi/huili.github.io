# sqlite3GenerateIndexKey
<pre>
 int sqlite3GenerateIndexKey(
  Parse *pParse,     /* Parsing context */
  Index *pIdx,       /* The index for which to generate a key */
  int iCur,          /* Cursor number for the pIdx->pTable table */
  int regOut,        /* Write the new index key to this register */
  int doMakeRec      /* Run the OP_MakeRecord instruction if true */
)
</pre>
函数功能：生成代码将会产生索引值并把它放在寄存器regOut中，pIdex所指的索引值是通过pTab 打开的表iCur 游标所指的索引并且指向需要需索引的条目。返回寄存器块(掌握着索引关键字的元素) 中第一个寄存器的编号，当这个例程返回的时候寄存器块已经被释放了。
