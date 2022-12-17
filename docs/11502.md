# 检查 MSSQL 中是否存在类型

> 原文：<https://dev.to/adamkdean/check-if-type-exists-in-mssql-2mme>

片段时间。使用以下简单查询检查 MSSQL 中是否存在某个类型:

```
IF TYPE_ID(N'[dbo].[udt_SomeCustomType]') IS NOT NULL
BEGIN
    -- type exists, do something here
END 
```

Enter fullscreen mode Exit fullscreen mode

当然，反过来说:

```
IF TYPE_ID(N'[dbo].[udt_SomeCustomType]') IS NULL
BEGIN
    -- type does not exist, do something here
END 
```

Enter fullscreen mode Exit fullscreen mode

更多的 SQL 片段将会出现。