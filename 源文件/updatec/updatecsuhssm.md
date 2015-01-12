# sqlite3Update函数说明
一个典型的update语句

    UPDATE OR IGNORE table_wxyz SET a=b, c=d WHERE e<5 AND f NOT NULL;
          \_______/ \________/     \______/       \________________/
           onError   pTabList      pChanges             pWhere
以下是在 SQLite 中常用的约束。

    NOT NULL 约束：确保某列不能有 NULL 值。
    DEFAULT 约束：当某列没有指定值时，为该列提供默认值。
    UNIQUE 约束：确保某列中的所有值是不同的。
    PRIMARY Key 约束：唯一标识数据库表中的各行/记录。


    CHECK 约束：CHECK 约束确保某列中的所有值满足一定条件。
冲突处理策略:

    replace
    ignore
    fail
    abort
    rollback
冲突处理策略紧跟在insert或update命令后并加上前缀or
