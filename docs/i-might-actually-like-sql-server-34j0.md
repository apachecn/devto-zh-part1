# 我可能真的喜欢 SQL Server

> 原文：<https://dev.to/funkysi1701/i-might-actually-like-sql-server-34j0>

这是怎么发生的我想我现在可能真的喜欢 SQL Server 了？

[![](img/a087578d2a523b1623af227f02ee5658.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LExUf-77--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2015/04/download.jpg)

我记得几年前，我曾经抱怨要编写一个包含连接的 SQL 查询。出于某种原因，当时我无法理解 JOINs。

今天是一个完全不同的故事。我编写了一个部署脚本，它完成了以下任务:

1.  丢弃了一个数据库，
2.  从备份文件中还原数据库，
3.  创建了一个数据库表，
4.  将一个表的内容复制到这个表中，
5.  禁用并重新启用了一些触发器，
6.  从表中删除了一些约束和列
7.  并重命名了一些列。

又不是我今天早上醒来就知道了 SQL Server。多年来，我一直在为我们的业务线数据库添加额外的特性和更改功能，随着时间的推移，我对 SQL 的信心也在增长。

对于上面的每一个步骤，我都在谷歌上查找 SQL 语法(每次我写 insert 或 update 语句时，我都会查找语法，总有一天它会留在我的脑子里！)我认为主要原因是，一旦你使用 SQL 一段时间，你就会看到它是如何工作的，并且可以把它分解成小步骤。

正如我前几天在博客上写的一样，我目前正在改进一个糟糕的数据库，今天我想测试一下部署过程。我所有的更改都在 SSDT 项目中，所以我备份了我的数据库，并试图发布。

错误！您的更改将导致数据丢失，不出所料，我已经预料到了这个错误。这件事的罪魁祸首是我写的一个触发器，但我直到那天结束时才发现。

当我在做备份的时候，我可以做我喜欢的事情，包括破坏性的改变。我尝试创建一个预部署脚本。我想，如果先运行，我可以说服 SSDT，它匹配 SQL Server，因此没有数据丢失。

这不起作用，但是我已经创建了我上面提到的脚本的开始。我经历了第 3 步和第 4 步。让我们首先尝试运行我的脚本，然后尝试运行我的 SSDT 项目的部署。为了实现这一点，我必须重新构建我的数据库几次，我用 SQL Management studio 手动做了几次，直到我想，我可以将它添加到我的脚本中——这是步骤 1 和 2 完成的。

然后，我得到了一些关于触发器的错误，所以我尝试禁用它们，并在完成后重新启用它们。这是第五步。

最后，我需要删除一些列，这导致了一个关于约束的错误。通过一些反复试验来找出我需要删除哪些约束，然后我可以删除这些列。

最后一步是重命名一些列。我想在这之后我可以从 visual studio 运行 publish。不，它仍然抱怨数据丢失。我放下另一个触发器，然后我可以运行一切没有任何错误。

Wohoo！我真聪明。下面是我的代码的简化版本

```
USE master -–can’t drop the database if its open!
–-Step 1
DROP DATABASE DBName

–-Step 2
RESTORE DATABASE DBName FROM DISK=’E:\DBName.bak’
WITH
MOVE ‘DBName_dat’ TO ‘C:\Program Files (x86)\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\DATA\DBName.mdf’,
MOVE ‘DBName_log’ TO ‘C:\Program Files (x86)\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\DATA\DBName.ldf’,
REPLACE,
STATS=10
USE DBName –-swap back to the database you just restored

–-Step 3
CREATE TABLE TName2 (
ID INT NOT NULL,
Name NVARCHAR(50) NOT NULL,
Address NVARCHAR(50) NOT NULL,
City NVARCHAR(50) NOT NULL,
Postcode NVARCHAR(50) NOT NULL)

-–Step 5
DISABLE TRIGGER TR_Trigger ON TName1
GO

–-Step 4
INSERT INTO TName2 (Id,Name)
SELECT Id, Name FROM TName1
GO

–-Step 6
ALTER TABLE TName1
DROP CONSTRAINT [DF_Id], [DF_Name]
GO
ALTER TABLE TName1
DROP COLUMN Id, Name
GO

–-Step 7
sp_RENAME ‘TName1.City’, ‘Area’ , ‘COLUMN’
GO

–-drop that last trigger
DROP TRIGGER TR_Trigger2
GO

–-Step 5
ENABLE TRIGGER TR_Trigger ON TName1 
```

Enter fullscreen mode Exit fullscreen mode