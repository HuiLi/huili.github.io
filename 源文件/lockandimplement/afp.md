# AFP Locking
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AFP（Apple Filing Protocol）是苹果文件协议，它是一个专有的网络协议，为Mac OS X和原始Mac OS提供文件服务。除了AFP，MacOSX也支持服务器消息块(SMB)、网络文件系统(NFS),文件传输协议(FTP)等几个文件服务。AFP支持先进的文件锁定，并且它是建立在苹果麦金塔什电脑（包括OS9 和OSX操作系统）之上的网络文件系统。AFP的第三方实现是有效的，但是SQLite中AFP锁是MacOSX操作系统特有的，不适合其它的unix平台。在代码实现过程中，如果我们不用mac去编译它，那么unix-afp的虚拟文件系统就不能用。另外，NFS锁也是MacOSX特有的锁模式。
