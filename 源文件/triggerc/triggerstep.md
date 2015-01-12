# Trigger的Step

    static TriggerStep *triggerStepAllocate()
分配空间来保存一个新的触发器步骤

    TriggerStep *sqlite3TriggerSelectStep()
构造触发器步骤去实现Select语句，并返回一个触发步骤指针。

    TriggerStep *sqlite3TriggerInsertStep()
构造触发器步骤去实现INSERT语句，并返回一个触发步骤指针。

    TriggerStep *sqlite3TriggerUpdateStep()
构造触发器步骤去实现UPDATE语句，并返回一个触发步骤指针。

    TriggerStep *sqlite3TriggerDeleteStep()
构造触发器步骤去实现DELETE语句，并返回一个触发步骤指针。

主流程图：
![](7.png)
