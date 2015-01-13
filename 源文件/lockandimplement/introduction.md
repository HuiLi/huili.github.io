# SQLite不同模式的锁与实现
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;文件中用于并发控制的文件加锁的方法有如下几种，支持基于数据库所在的文件系统自动选择适当的加锁风格。<br>
——POSIX locking (the default)，<br>
——No locking，<br>
——Dot-file locking，<br>
——flock() locking，<br>
——AFP locking (OSX only)，<br>
——Named POSIX semaphores (VXWorks only)，<br>
——proxy locking. (OSX only)
