# sqlite3GenerateRowDelete
<pre>
void sqlite3GenerateRowDelete(
  Parse *pParse,     /* Parsing context */
  Table *pTab,       /* Table containing the row to be deleted */
  int iCur,          /* Cursor number for the table */
  int iRowid,        /* Memory cell that contains the rowid to delete */
  int count,         /* If non-zero, increment the row change counter */
  Trigger *pTrigger, /* List of triggers to (potentially) fire */
  int onconf         /* Default ON CONFLICT policy for triggers */
)
</pre>
函数有以下几部分实现：
<pre>
  1）虚拟数据库引擎保证在这这个阶段已分配。
  2）寻找游标iCur 来删除行，如果该行不存在(如果触发器程序已经删除它，这种情况会发生)，不要尝试去删除它或解除任何的DELETE触发器
  3）如果任何触发器被解除，分配一些寄存器来使用旧的触发器。
  4）可以在这使用临时的寄存器，也可以尝试避免复制rowid 寄存器的内容。
  5）填充OLD 伪表寄存器数组，这些值将被存在的BEFORE 和AFTER寄存器使用。
  6）调用BEFORE DELETE 触发器程序。
  7）在此寻找要删除行的游标，这可能是BEFORE 触发器代码已经移除了要删除的行，不要尝试去第二次删除该行，不要解除AFTER 触发器。
  8）执行外键约束过程，该调用将检查与本表有关的任何外键约束，该调用不会影响删除行。
  9）删除索引和表条目，如果pTab所指的表是一个视图(在这个情况下对DELETE 语句唯一的影响是解除INSTEAD OF 触发器) 跳过这一步。
  10）通过引用被删除行的外键执行任何处理行 (可能在其他表中)  的CASCADE,SET NULL 或SET DEFAULT 操作
  11）调用AFTER DELETE触发器程序。
  12）如果行在调用BEFORE 触发器之前已经删除跳转到这里。或者一个触发器程序抛出了一个RAISE(IGNORE) 异常。</pre>
