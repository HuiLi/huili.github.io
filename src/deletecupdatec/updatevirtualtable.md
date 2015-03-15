# updateVirtualTable
<pre>
 static void updateVirtualTable(
  Parse *pParse,       /* The parsing context */
  SrcList *pSrc,       /* The virtual table to be modified */
  Table *pTab,         /* The virtual table */
  ExprList *pChanges,  /* The columns to change in the UPDATE statement */
  Expr *pRowid,        /* Expression used to recompute the rowid */
  int *aXRef,          /* Mapping from columns of pTab to entries in pChanges */
  Expr *pWhere,        /* WHERE clause of the UPDATE statement */
  int onError          /* ON CONFLICT strategy */
)
</pre>

函数功能介绍：<br>
生成虚拟表更新代码, 创建一个临时表，该表包含要修改的每一行<br>
A）该行最初的ROWID。<br>
B）正后的行ROWID。<br>
C）行中每一列的内容。<br>
然后我们遍历这个临时表，这个临时表中的每一行调用VUpdate。（(注1)其实，如果我们事先知道(A) 始终与(B) 是一样的，我们仅存储(A)，在调用VUpdate之前，当把它从临时表中弄出来的时候复制A。函数由以下几部分组成：<pre>
1）构建SELECT语句，会发现所有更新的行的新值。
2）创建临时表到更新结果被保存。
3）填补临时表
4）生成代码来扫描临时表和调用VUpdate。
5）清理
</pre>
