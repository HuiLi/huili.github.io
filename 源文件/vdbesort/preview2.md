# vdbesort.c源码概览
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;Vdbesort.c中是实现vdbesorter的实例的代码，vdbesorter和vdbecursor相配合来完成对大量key值的排序，例如在在为一个太大以致于不能完全放到内存中的表建立索引时就需要排序操作。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;	该文件虽然有将近一千多行的源代码，但是其代码结构并不复杂，总体上而言，整个源代码文件就由4个结构体的定义和25个功能各不相同的函数的定义组成，并且函数名起的都易于理解、见名知意，另外，尽管后定义的函数可能会调用到先定义的函数，但是调用方式简单，调用次数少，所以25个函数间的模块性并不是太明显，它们之间的联系并不是太复杂。
