# 校验
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;跟在WAL头上的是0到多个框。每个框由一个24字节的框头（frame-header）和一个页大小的页数据组成。框头是6个大端的32位无符号整数，如下：
* 0: Page number.

* 4: For commit records, the size of the database image in pages after the commit. For all other records, zero.
* 8: Salt-1 (copied from the header)

* 12: Salt-2 (copied from the header)

* 16: Checksum-1.

* 20: Checksum-2.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当一个框满足下面条件时，才被认为是有效的：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.在框头中Salt-1和salt-2的值跟在wal头里面的一致<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.框头的最后的8个字节的校验和完全匹配一下内容的校验结果包括在WAL头上最开始的8个字节和当前框的所有内容。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果WAL文件的开头4个字节是0x377f0683，那么校验和就使用32位的大端计算，如果是0x377f0682，则用小端计算。校验和始终存储在框头使用大端模式存储而无论使用什么模式计算的。校验和的计算方法如下：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;for i from 0 to n-1 step 2:<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;s0 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;+= x[i] + s1;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;s1 += x[i+1] + s0;<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;endfor<br>
<img src="check.jpg"><br>
