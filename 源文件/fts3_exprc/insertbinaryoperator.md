#  insertBinaryOperator()函数

    static void insertBinaryOperator(Fts3Expr **ppHead,Fts3Expr *pPrev,Fts3Expr *pNew)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;函数的功能是插入二元操作，ppHead包含了一个指针指向了一个正在被解析的查询表达树的顶部，pPrev则是一个最近插入树中的结点，这个函数增加了pNew，它总是一个二元操作结点，基于和pNew相对的优先级以及现存于树中结点的优先级插入树中，这可能会导致树头结点的改变，pHead会指向新的根节点。
