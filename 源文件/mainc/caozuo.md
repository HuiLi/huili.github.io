# Main.c
# 操作控制
        操作控制基本上都是监视数据库的连接和事务的函数。由一下三个函数构成：
        提交钩子：sqlite3_commit_hook()监视连接上的事务提交事件。
	    回滚钩子：sqlite3_rollback_hook()监视连接上的事务回滚事件。
	    更新钩子：sqlite3_update_hook()监视连接上的事务更新事件。
