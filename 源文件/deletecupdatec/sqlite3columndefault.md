# sqlite3ColumnDefault
<pre>
void sqlite3ColumnDefault(Vdbe *v, Table *pTab, int i, int iReg){
  assert( pTab!=0 );
  if( !pTab->pSelect ){
    sqlite3_value *pValue;
    u8 enc = ENC(sqlite3VdbeDb(v));
    Column *pCol = &pTab->aCol[i];
    VdbeComment((v, "%s.%s", pTab->zName, pCol->zName));
    assert( i<pTab->nCol );
    sqlite3ValueFromExpr(sqlite3VdbeDb(v), pCol->pDflt, enc,
                         pCol->affinity, &pValue);
    if( pValue ){
      sqlite3VdbeChangeP4(v, -1, (const char *)pValue, P4_MEM);
    }
    if( iReg>=0 && pTab->aCol[i].affinity==SQLITE_AFF_REAL ){
      sqlite3VdbeAddOp1(v, OP_RealAffinity, iReg);
    }
  }
}
</pre>

函数介绍：<br>
最近编码指令是一个OP_Column来检索pTab 所指表中第i 列，这个过程将OP_Column的P4  参数(如果有的话) 设定为默认值，在列定义的DEFAULT 子句中指定列的默认值。<br>
当表建立起来，通过用户提供，或通过ALTER TABLE 命令后来添加到表定义中，如果是后者，那么磁盘上btree表的行记录可能不包含列值和默认值，从 OP_Column 指令的P4 参数所取的值，则返回代替，如果是前者，那么所有行记录对于列有一个值，并且P4的值不是必须的。ALTER TABLE命令创建的列定义可能只有文字指定默认值:一个数字,null或一个字符串。<br>
如果提供了一个更复杂的默认表达式值,当ALTER TABLE语句执行，一个文字值写入sqlite_master table的时候评估。<br>
因此,如果列的默认值是一个文本数字,字符串或null，P4参数是必要的。sqlite3ValueFromExpr()函数可以将这些类型的表达式转换为sqlite3_value对象。如果参数iReg非负,在寄存器iReg上编码一个OP RealAffinity指令。这是当一个相等的整数值存储在一个8字节浮点值的地方,以节省空间。
