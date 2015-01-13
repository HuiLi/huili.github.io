# wherecSummary
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SQLite中的查询优化虽然较为简单，但也对大多数情况进行了分析优化。在SQLite查询优化中，很多情况下是需要使用ANALYZE进行分析、统计的，这会让SQLite较为容易的选择正确的优化策略，并且会让SQLite选择更加优秀的优化策略。同时SQLite支持用户自定义一些优化策略（如：多重索引选择，自动索引等），用户在使用SQLite时，如果遇到可以自定义的情况，请根据实际情况进行选择，这也使得SQLite优化策略显得较为灵活。
