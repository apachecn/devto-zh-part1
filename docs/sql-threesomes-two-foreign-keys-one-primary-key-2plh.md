# Sql 三人组..两个外键，一个主键

> 原文：<https://dev.to/adamkdean/sql-threesomes-two-foreign-keys-one-primary-key-2plh>

所以我在这里，为我自己的事业编码，做一个守法的编码员，当突然出现一个相当麻烦的小问题，真的让我很困扰。我为我的绝密项目制定了一个令人惊叹的计划，一个真正令人惊叹的智能数据库设计，通过关系将表中的行链接到完全相同的表中的行，单向关系从 A->B。我所需要的是一种在删除时级联它们的方法，换句话说，确保当一行被删除时，每个关系(关系表中的)也一起被删除。

事情有这么简单吗？MSSQL 决定告诉我，因为一个字段有主键(PK)的外键(FK)，所以另一个字段不能也有主键的 FK，这是不行的(OK)。那是！好..

幸运的是，SQL 有称为触发器的东西，它们实际上非常有用。下面的 SQL 创建了一个触发器，该触发器从第二个表中删除所有行，这些行与从第一个表中删除的行具有相同的 id:

```
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
-- ==============================================
-- Author: Adam K Dean
-- Website: http://imdsm.blogspot.com/
-- Create date: 12/09/10
-- Description: Deletes relationships on deletion
-- of row in the main database. Replaces the need
-- for two foreign keys on the same primary key
-- which is unfortunately impossible.
-- ==============================================
ALTER TRIGGER DeleteRelationships ON t_Sites AFTER DELETE
AS
BEGIN

 DELETE FROM t_Relationships 
 WHERE ReferrerId IN (SELECT Id FROM Deleted)
  OR TargetId IN (SELECT Id FROM Deleted)

END
GO 
```

Enter fullscreen mode Exit fullscreen mode

不知何故，我设法做到了这一点，找到片段并测试它们，因为让我们面对它，SQL 示例有这么多的选项，它就像一堵每秒 150 页的文本墙，并不像查看一些中国黑客代码并挑选出您需要的片段那样容易使用。

看起来,`Deleted`对象指的是被删除的行，剩下的就很简单了。

希望这能帮助你，或者未来的我，如果你和我一样正遭受着杰里米·斯奎尔的问题！