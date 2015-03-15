# whereRangeScanEst函数
此函数用来估计扫描一定范围数值的索引中，将会访问到的行数。这个范围可能会有一个上限，一个下限，或者两者都有。WHERE子句项中，用pLower和pUpper来表示设定的上下边界。例如，假设p索引在t1(a)


        ... FROM t1 WHERE a > ? AND a < ? ...
                        |_____|   |_____|
                           |         |
                         pLower    pUpper


如果上限和下限中的任意一个不存在，则WhereTerm中对应的项用空值来替换。
