# 函数sqlite3ExpirePreparedStatements
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;将每一个和数据库连接的处于准备状态的语句标记为过期的状态。一个过期状态的语句意味着重新编译这个语句是被推荐的。一些事情的发生使得数据库语句过期。移除用户自定义的函数或者排序序列，改变一个授权功能这些都可以使准备状态的语句变成过期状态。
