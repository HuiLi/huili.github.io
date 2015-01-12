# Where.c代码解析
Where.c中的核心函数是sqlite3WhereBegin函数，SQLite在该函数中完成了所有的查询优化及查询处理代码的生成。其查询优化的方式是循环每个需要查询的表，分析对于每个表Where中查询条件是否有可用的索引，生成的操作码结构如下：

    foreach row1 in t1 do       \    Code generated
        foreach row2 in t2 do      |-- by sqlite3WhereBegin()
            foreach row3 in t3 do   /
            ...
            end                     \    Code generated
        end                        |-- by sqlite3WhereEnd()
    end                         /
