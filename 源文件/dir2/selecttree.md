# SELECT查询树
&nbsp;&nbsp;&nbsp;&nbsp;Sqlite3SelectNew()对整个SQL语句进行语法检查，实质上生成一个包含所有信息的SELECT语法树，SELECT语法树用SELECT结构体表示。pEList（ExprList类型）是输出结果列语法树，pSrc（SrcList类型）为FROM子句语法树；pWhere（Expr类型）为WHERE部分的语法树，pGroupBy（ExprList类型）为GROUP BY的语法树，pHaving（Expr类型）为Having的语法树，pOrderBy（ExprList类型）为ORDER BY的语法树，pLimit（Expr类型）为Limit语法树，pOffset（Expr类型）为offset语法树。最终这些语法树在sqlite3SelectNew()中汇总到SELECT结构体，生成一棵查询语法树。
