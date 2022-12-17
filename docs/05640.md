# 错误报告#1 -较长查询中的 GETUTCDATE()

> 原文：<https://dev.to/horia141/bug-report-1---getutcdate-in-longer-queries-48o9>

这是我在工作中遇到的 bug 系列文章的第一篇[1]。这是我在博客上尝试的新东西，和星期五爆炸系列一起，目前在第四周。外面有很多虫子。但是我想强调那些足够普通的*，这样解决方案对其他人也有用，足够有趣的*，它们值得一写。**

 **今天的 bug 与 [`GETUTCDATE()`](https://docs.microsoft.com/en-us/sql/t-sql/functions/getutcdate-transact-sql) 、SQL Server 函数有关，以及我在 StackOverflow 的日常计划任务中是如何使用它的。这项任务或多或少会查看过期两周以上的活跃广告活动。一旦发现它，*将*它们归档，这基本上意味着将它们的 *ArchivedDate* 字段设置为当前时间，*和*在事件日志中插入一个*归档的*类型的事件。我编写这个程序的天真方式看起来或多或少是这样的:

```
insert into CampaignHistory (CampaignId, Timestamp, HistoryType)
select Id, GETUTCDATE(), 6 /* Archived */
from Campaigns
where ArchivedDate is null
and DATEDIFF(day, EndDate, GETUTCDATE()) > 14;

update Campaigns
set ArchivedDate = GETUTCDATE()
where ArchivedDate is null
and DATEDIFF(day, EndDate, GETUTCDATE()) > 14; 
```

Enter fullscreen mode Exit fullscreen mode

这个问题是从上面的英语散文忠实的翻译。它通过我们使用的微型表单 [Dapper](https://stackexchange.github.io/Dapper/) 执行。

我非常确定`GETUTCDATE()`在整个查询中会是相同的值，因为我知道在像第一个`insert`这样的单个查询中它会是相同的，即使多行会受到影响。但是事实并非如此，两个查询返回的值是不同的。

这意味着由第一个查询中的`select`产生的行集合可能与`update`查询中的不同。更准确地说，它们将是其中的一个子集，因为在它们的执行之间可能会经过足够长的时间，当第一个查询被执行时，它们的结束日期还不到两周，当第二个查询被执行时，它们就变成了结束日期。

因此，我们最终获得了一堆已归档的活动，但没有相应的归档事件。一旦被检测到，一旦我发现了这个问题，修复就非常简单了。我只需要为所有比较使用的日期使用一个确定性的值，所以这两个行集是相同的。代码变成了:

```
declare @cutoff datetime = GETUTCDATE();

insert into CampaignHistory (CampaignId, Timestamp, HistoryType)
select Id, @cutoff, 6 /* Archived */
from Campaigns
where ArchivedDate is null
and DATEDIFF(day, EndDate, @cutoff) > 14;

update Campaigns
set ArchivedDate = @cutoff
where ArchivedDate is null
and DATEDIFF(day, EndDate, @cutoff) > 14; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

[1]在某个时候，我可能会在这个 0 人社区上发布我遇到的更大的昆虫的照片，作为一个玩笑。**