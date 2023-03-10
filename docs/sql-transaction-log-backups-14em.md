# SQL 事务日志备份

> 原文：<https://dev.to/funkysi1701/sql-transaction-log-backups-14em>

像许多数据库管理员一样，我花大量时间维护我的 SQL Server 备份。

在 SQL Server 中，我维护完整备份和事务日志备份。我经常还原我的完整备份，但直到最近，我从未还原过事务日志备份。所有备份策略都只有在您上次测试恢复过程时才有效。

## 那么什么是事务日志备份呢？

事务日志备份包含自上次完全备份以来生成的所有事务日志记录，用于将数据库恢复到特定时间点(通常是灾难发生前的时间)。因为这些是增量的，所以如果您想要将数据库还原到某个特定的时间点，您需要拥有重播到该时间点的数据库更改所必需的所有事务日志记录。

## 如何进行还原。

首先在 SQL Management Studio 中右键单击数据库，并选择恢复数据库。然后，您应该会看到一个类似这样的屏幕。

[![](img/7609970cb4ab139d53f9231588171eda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CtGlUEXT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2016/02/restore1.jpg%3Fresize%3D768%252C640%26ssl%3D1)

在源中，单击……以允许您选择备份文件。

现在我通常只选择一个文件，即*。bak 文件。请选择*。bak 和所有的*。trn 文件也一样。SQL Server 运行几分钟后(时间取决于事务文件的数量和服务器/磁盘速度等)，恢复计划部分应该会填满文件。

在“目标数据库”框中，键入要还原的数据库的名称。我建议使用不同的名称来避免覆盖原始数据库，我通常会在名称后面附加 Test 或 datetime。

在我的测试服务器上，我需要在选项屏幕上取消选中“进行结尾日志备份”选项，然后才能执行恢复。

现在，您可以选中“restore plan”部分中的复选框，或者(更有趣的是)单击“timeline”按钮来选择要恢复到的时间点。

[![](img/cfa07951b6ada78fc477dc4883af9c2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MRzb1MKY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2016/02/restore2.jpg%3Fresize%3D768%252C457%26ssl%3D1)

您可以用鼠标选择时间点，也可以在时间文本框中指定确切的时间点。或者，你可以只选择最近的点，可能是灾难发生时最有可能的选项。

现在我已经在我的测试服务器上试着这么做了，我觉得更有信心，当灾难真的来临时，我可以快速轻松地恢复东西。

## 您应该多久运行一次事务备份？

这个问题的答案取决于您的数据有多重要。直到最近，我一直是 15 分钟跑一次，我已经增加到每 5 分钟一次，但是我已经看到了每分钟[跑一次的建议。您的数据越重要，就应该越频繁地运行它们。](https://www.brentozar.com/archive/2014/02/back-transaction-logs-every-minute-yes-really/)