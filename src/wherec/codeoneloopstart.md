# codeOneLoopStart函数
循环生成第iLevel层的OPCode。

根据五种查询策略生成OPCode，包括：

1. RowId等值查询；
2. RowId范围查询；
3. 使用索引等值或范围查询；
4. OR查询；
5. 全表扫描；
6. 基于虚表（Virtual Table）的扫描。


虚表处理：

    if(  (pLevel->plan.wsFlags & WHERE_VIRTUALTABLE)!=0 ){
        /* Case 0:  The table is a virtual-table.  Use the VFilter and VNext to access the data.
        表是一个虚拟表。使用VFilter和VNext访问数据
        */
        ......
        sqlite3VdbeAddOp2(v, OP_Integer, pVtabIdx->idxNum, iReg);
        sqlite3VdbeAddOp2(v, OP_Integer, j-1, iReg+1);
        sqlite3VdbeAddOp4(v, OP_VFilter, iCur, addrBrk, iReg, pVtabIdx->idxStr, pVtabIdx->needToFreeIdxStr ? P4_MPRINTF : P4_STATIC);

RowId等值查询：

    if( pLevel->plan.wsFlags & WHERE_ROWID_EQ ){
        /* Case 1:  We can directly reference a single row using an
        **  equality comparison against the ROWID field.  Or
        **  we reference multiple rows using a "rowid IN (...)" construct.
        可以直接引用一个单行对ROWID字段使用相等的比较。
    或者使用引用多个行“rowid(…)IN”结构。
    */
    ......
    sqlite3VdbeAddOp2(v, OP_MustBeInt, iRowidReg, addrNxt);
    sqlite3VdbeAddOp3(v, OP_NotExists, iCur, addrNxt, iRowidReg);
    sqlite3ExprCacheStore(pParse, iCur, -1, iRowidReg);

RowId非等值扫描：

    else if( pLevel->plan.wsFlags & WHERE_ROWID_RANGE ){
    /* Case 2:  We have an inequality comparison against the ROWID field.
    对ROWID字段非等值的比较
    */
    ......
    sqlite3VdbeAddOp3(v, aMoveOp[pX->op-TK_GT], iCur, addrBrk, r1);

使用索引的范围或等值查询：

    else if( pLevel->plan.wsFlags & (WHERE_COLUMN_RANGE|WHERE_COLUMN_EQ) ){
        /* Case 3: A scan using an index.
        where子句可能有0个或者多个相等关系（"==" or "IN" ）涉及到N个最左连接索引它可能也有不等关系(>, <, >= or <=)在索引集中紧跟着Ｎ相等关系。只有最右连接集才可以成为相等，剩余的必须用于 "==" and "IN" 。
    例如以(x,y,z)建立索引。下面所有的子句都是最优的。
    　　　　　        　　x=5
        **           x=5 AND y=10
        **           x=5 AND y<10
        **           x=5 AND y>5 AND y<10
        **           x=5 AND y=5 AND z<=10**

OR查询：

    if( pLevel->plan.wsFlags & WHERE_MULTI_OR ){
        /* Case 4:  Two or more separately indexed terms connected by OR
        **两个或两个以上的独立索引术语或连接


全表查询：

    else
    {
        /* Case 5:  There is no usable index.  We must do a complete scan of the entire table.
        没有可用的索引。我们必须完整的扫描整个表。
        */
        static const u8 aStep[] = { OP_Next, OP_Prev };
        static const u8 aStart[] = { OP_Rewind, OP_Last };
        assert( bRev==0 || bRev==1 );
        assert( omitTable==0 );
        pLevel->op = aStep[bRev];
        pLevel->p1 = iCur;
        pLevel->p2 = 1 + sqlite3VdbeAddOp2(v, aStart[bRev], iCur, addrBrk);
        pLevel->p5 = SQLITE_STMTSTATUS_FULLSCAN_STEP;
    }
