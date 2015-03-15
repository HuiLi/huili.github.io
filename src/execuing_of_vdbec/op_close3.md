# OP_Close3
下图是OP_Close的内容:

 ![]( 4-5-31.jpg)
OP_Close操作符就是根据p1的值确定游标，然后释放这个游标并把游标所占内存充0。pc加1为28，下一个要执行的是aOp[28] OP_Integer。
