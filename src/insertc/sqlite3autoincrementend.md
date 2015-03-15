# sqlite3AutoincrementEnd()
输入参数：Parse *pParse
返回值：void
函数作用：这个例程产生的代码必须把自增的最大行id的值写回到sqlite_sequence寄存器中。仅在这个  “exit”代码之前，每一条可能把一个INSERT写入到一个自增表（直接或间接地通过寄存器）中需要调用这个例程。
解释：主要是结束增长，获取增长的数据，作为要使用的数据插入到数据表当中。
