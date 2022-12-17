# 检查 SQLServer update 触发器中的列是否已更新

> 原文：<https://dev.to/ravenous_baboon/checking-if-the-column-was-updated-inside-sqlserver-update-trigger>

有三种方法可以检查触发器中的列是否被更新:

1.  检查 UPDATE(列名)的值
2.  检查 COLUMNS_UPDATED()的值和更新的列的整数掩码(也适用于多个列)
3.  检查列是否出现在插入的表中——如果存在(从 inserted 中选择 Column_Name)

然而，这三者的工作方式不同。

示例:
我们有一个表 dbo。我们想检查它的第一列“姓氏”的更新。我们将创建一个包含所有三种方法的触发器。它将为每个满足的条件打印一条消息:

```
CREATE TRIGGER [dbo].[CustomersUpdateTrigger]
ON [dbo].[Customers]
AFTER UPDATE
AS
  BEGIN
      IF UPDATE(LastName)
        PRINT 'UPDATE(LastName)'

      IF ( COLUMNS_UPDATED () & 1 = 1 )
        PRINT 'COLUMNS_UPDATED ( )  & 1'

      IF EXISTS(SELECT LastName
                FROM   inserted)
        PRINT 'EXISTS(SELECT LastName FROM inserted)'
  END 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们对 Customers 表运行一个相当具体的 update 语句:

```
UPDATE dbo.Customers
SET    lastname = 'SomeNewLastName'
WHERE  0 = 1 
```

Enter fullscreen mode Exit fullscreen mode

显然，不会更新任何行。我们将会看到多少条消息？

```
UPDATE(LastName)
COLUMNS_UPDATED ( )  & 1

(0 row(s) affected) 
```

Enter fullscreen mode Exit fullscreen mode

结论:如果我们检查的列在更新列表中，则检查 UPDATE()和 COLUMNS_UPDATED()返回 true，而不考虑实际的数据更新。对于 IF EXISTS(SELECT Column _ Name FROM inserted)检查，我们需要更新一些实际数据。

微软文档参考资料:
[更新()](https://docs.microsoft.com/en-us/sql/t-sql/functions/update-trigger-functions-transact-sql)
[栏目 _ 更新](https://docs.microsoft.com/en-us/sql/t-sql/functions/columns-updated-transact-sql)