# SQL 通过分组方式获取最新记录

> 原文：<https://dev.to/adamkdean/sql-get-most-recent-records-with-group-by-2lj8>

下面是牛逼的 SQL。我现在就告诉你，我不喜欢 SQL。我很佩服，但是不喜欢。所以 SQL 在这方面有点像 Alan Sugar。但是我必须要做的，或者说，我刚刚做的，是非常重要和至关重要的事情，以使我的中等秘密项目更加有效。

我有两个表，hosts 和 records，对于 hosts 表中的每个主机行，records 表中都有一组记录行。我需要 SQL 获取由日期字段定义的所有记录，这些记录要么是 X 分钟之前的，要么是 X 分钟之后的。

我可以在应用程序中很容易地做到这一点，我也一直在这样做，但是当我将 5000 台主机放入数据库进行测试时，它无法完全保持它，因为每次迭代它都必须取出 5000 台主机并单独获取最新记录。因此，我缩减到 1000 台主机来测试我的系统，并设法获得大约 90%的维护，也就是说，跟上 90%的主机。

问题是，当它检查是否有主机有旧记录时，顺便说一句，这是一种非常低效的方法，它不检查列表的开始，这恰好是第一个检查的，也是第一个超过阈值的。

所以 SQL 语句。在来自#lifestinks 的@小飞侠的帮助下，在被#sql 的某人告知这是不可能的，以及米纳(没有固定互联网住所)的醉醺醺的不回复，我想出了一些 sql，这将完全得到我想要的。

现在它不会向我返回记录的所有细节，只返回最近记录的 ID 和日期。我会用普通的 SQL 语句给你，不带 T-SQL 语句:

```
SELECT HostId, MAX(Hostname) AS Hostname, MAX(CheckDate) AS CheckDate, MAX(Frequency) AS Frequency
FROM vw_HostRecords
GROUP BY HostId
HAVING (DATEADD(minute, MAX(Frequency), MAX(CheckDate)) < GETDATE()) 
```

Enter fullscreen mode Exit fullscreen mode

现在你有了它，可怕却美丽的 SQL 代码，让我如释重负。

享受吧，未来的我！