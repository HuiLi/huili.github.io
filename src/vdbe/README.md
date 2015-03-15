# vdbe.c文件内容
vdbe.c文件中存放着VDBE的执行方法（sqlite3VdbeExec），这是VDBE的核心，也是SQLite的核心，SQL解析器生成一个程序然后由VDBE执行SQL语句的工作。VDBE程序在形式上类似于汇编语言。VDBC程序由一系列线性操作组成，每个操作都有1个操作码和5个操作数，操作数P1,P2,P3是整数，操作数P4是一个以null结尾的字符串，操作数P5是一个无符号字符。sqlite3VdbeExec函数用于解析VDBE程序指令,但  是要建立一个程序指令还需要其它文件里的函数的帮助和支撑。
        下面是sqlite3VdbeExec函数的说明：
```
    int sqlite3VdbeExec( Vdbe *p) {                    /* The VDBE */
      int pc=0;                  /* 程序计数器 */
      Op *aOp = p->aOp;          /* p->aOp的复本 */
      Op *pOp;                   /* 当前操作码 */
      int rc = SQLITE_OK;        /* 函数返回值 */
      sqlite3 *db = p->db;       /* 数据库 */
      Mem *aMem = p->aMem;       /* p->aMem的复本 */
      Mem *pIn1 = 0;             /* 第一次输入的参数 */
      Mem *pIn2 = 0;             /* 第二次输入的参数 */
      Mem *pIn3 = 0;             /* 第三次输入的参数 */
      Mem *pOut = 0;             /* 输出的参数*/
      int iCompare = 0;          /*存放操作码OP_Compare的操作结果*/
      int *aPermute = 0;         /*操作码OP_Compare使用的数组。*/
      sqlite3VdbeEnter(p);       /*初始化虚拟机程序p的环境*/
      p->rc = SQLITE_OK;
      p->pResultSet = 0;
      db->busyHandler.nBusy = 0;
      CHECK_FOR_INTERRUPT;
      sqlite3VdbeIOTraceSql(p);
      for(pc=p->pc; rc==SQLITE_OK; pc++){
        pOp = &aOp[pc];
      	if( pOp->opflags & OPFLG_OUT2_PRERELEASE ){
          assert( pOp->p2>0 );
          assert( pOp->p2<=p->nMem );
          pOut = &aMem[pOp->p2];
          memAboutToChange(p, pOut);
          VdbeMemRelease(pOut);
          pOut->flags = MEM_Int;
        }
        switch( pOp->opcode ){
    		//switch语句,每一个case都是在VDBE里执行一个单独的指令，一共155个。
    		……
    	}
    }

```

