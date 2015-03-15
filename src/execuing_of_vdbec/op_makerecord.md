# OP_MakeRecord
下图是OP_MakeRecord的内容:

 ![]( 4-5-29.jpg)
OP_MakeRecord操作码的功能就是尝试用现有记录中的元素去填充，然后计算存放这些记录所需要的内存，再把记录写入。pc加1为26，下一个要执行的是aOp[26] OP_Insert。
