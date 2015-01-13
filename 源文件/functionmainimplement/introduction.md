# 功能的主要实现
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该文件对于功能的实现主要是通过一系列的函数和结构体。该文件的主线函数是sqlite3\_os\_init()，初始化操作系统接口，这个程序为所有类Unix操作系统的VFS实现注册。这个程序和紧随其后的sqlite3\_os\_end()程序是本文件中对其他文件可见的唯一程序，这个程序在SQLite初始化时便会通过单线程调用。<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个文件挂载了一些VFS（虚拟文件系统），每个VFS都包含一套系统调用的接口：<br>
\#define UNIXVFS(VFSNAME, FINDER) {<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    3,                    /\* iVersion \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    sizeof(unixFile),     /\* szOsFile \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    MAX_PATHNAME,         /\* mxPathname \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    0,                    /\* pNext \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    VFSNAME,              /\* zName \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    (void\*)&FINDER,       /\* pAppData \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixOpen,             /\* xOpen \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixDelete,           /\* xDelete \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixAccess,           /\* xAccess \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixFullPathname,     /\* xFullPathname \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixDlOpen,           /\* xDlOpen \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixDlError,          /\* xDlError \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixDlSym,            /\* xDlSym \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixDlClose,          /\* xDlClose \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixRandomness,       /\* xRandomness \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixSleep,            /\* xSleep \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixCurrentTime,      /\* xCurrentTime \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixGetLastError,     /\* xGetLastError \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixCurrentTimeInt64, /\* xCurrentTimeInt64 \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixSetSystemCall,    /\* xSetSystemCall \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixGetSystemCall,    /\* xGetSystemCall \*/<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    unixNextSystemCall,   /\* xNextSystemCall \*/<br>
  }<br>
这是一套虚拟文件系统的标准系统调用接口，为一个sqlite3_vfs对象定义了一个初始化器，通过这些接口可以操作VFS下的文件。而挂载是在如下代码中完成的：<br>
  for(i=0; i<(sizeof(aVfs)/sizeof(sqlite3_vfs)); i++){<br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3\_vfs\_register(&aVfs[i], i==0);<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return SQLITE_OK; <br>
}<br>
所有为Unix的默认VFS都包含了如下数组：<br>
static sqlite3_vfs aVfs[]<br>
初始化的虚拟文件系统列表在这个数组中。<br>
而在如下代码中，定义了一套标准的系统调用接口组件：<br>
static struct unix_syscall {<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;const char \*zName;            /\* Name of the       system call \*/<br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3\_syscall\_ptr pCurrent; /\* Current value of the system call \*/	<br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sqlite3\_syscall\_ptr pDefault; /\* Default value \*/ <br>
}<br>
我们顺着主要的接口之一unixOpen()，得到其主线流程如下图所示：<br>
<img src="p1.png">
<div align="center">图1.1 unixOpen()程序流程图</div>
