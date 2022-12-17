# 从所有表中查找列

> 原文：<https://dev.to/adamkdean/find-column-from-all-tables-50gm>

今天一个非常有用的 SQL 片段，感谢[丹尼·道斯](http://twitter.com/noirenex)和 [SQLAuthority](http://blog.sqlauthority.com/2008/08/06/sql-server-query-to-find-column-from-all-tables-of-database/) 。

以下查询将列出引用特定列名的所有表:

```
SELECT t.name AS table_name,
SCHEMA_NAME(schema_id) AS schema_name,
c.name AS column_name
FROM sys.tables AS t
INNER JOIN sys.columns c ON t.OBJECT_ID = c.OBJECT_ID
WHERE c.name LIKE '%EmployeeID%'
ORDER BY schema_name, table_name; 
```

Enter fullscreen mode Exit fullscreen mode

只需将`%EmployeeID%`更改为您正在寻找的列名。