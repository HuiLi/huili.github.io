# stat3Init函数，stat3Push函数，stat3Get函数
实现了stat3_init(C,S)

SQL功能，这两个参数分别是在表或者索引中的行数和累计的采样数。这个过程分配并初始化Stat3Accum 对象的每一个属性。返回值是 Stat3Accum 对象.

stat3Push实现了stat3_push(nEq,nLt,nDLt,rowid,P) SQL的功能，这些参数描述了一个关键字的实例。这个过程做出决定关于是否保留sqlite_stat3表的关键字。

stat3Get实现了stat3_get(P,N,...) SQL 语句功能的实现，用于查询结果。返回的是sqlite_stat3的第N行。N是在0 到 S-1之间，s是样本数。返回的值根据的是参数的值。

如
  argc==2    结果:  rowid

  argc==3    结果:  nEq

  argc==4    结果:  nLt

  argc==5    结果:  nDLt
