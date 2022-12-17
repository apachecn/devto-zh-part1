# 检查 SQL 中是否存在临时表

> 原文：<https://dev.to/adamkdean/check-if-temp-table-exists-in-sql-2ijc>

我将开始张贴一些 SQL 片段，或者是我每天学到的东西，或者是我已经坐了几年的东西。

今天的任务是检查一个临时表是否存在(然后删除它。)

```
IF OBJECT_ID('Tempdb..#tablename') IS NOT NULL
BEGIN
    -- do something here, like say, drop that table?
    DROP TABLE #tablename;
END 
```

Enter fullscreen mode Exit fullscreen mode