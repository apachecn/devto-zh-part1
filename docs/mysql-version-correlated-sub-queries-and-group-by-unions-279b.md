# MySql 版本“关联子查询和按联合分组”

> 原文：<https://dev.to/adamkdean/mysql-version-correlated-sub-queries-and-group-by-unions-279b>

如果你看了这篇 SQL 文章[旧链接被删除]，你可能会对我糟糕的编码实践印象深刻，或者侮辱我。仅供参考，它确实可以在不到 1 秒的时间内运行 1000 行。当我使用 MsSql(在我迁移到 CentOS/Mono/MySql 之前)时，我有时不得不付出巨大的努力来完成工作，但是使用 MySql，这一切似乎太容易了..因此，从您在上一篇文章中看到的巨大 SQL 中，我可以用下面的 SQL 在相同的表上完成所有这些操作(除了一个更改之外):

```
SELECT t_Hosts.Id AS Id, t_Records.Id AS RecordId, Hostname, 

Frequency, PingStatus, PingLatency, HttpStatusCode, 

HttpOnlineStatus, HttpLatency, CheckDate

FROM t_Hosts 

LEFT JOIN t_Records ON t_Hosts.Id = t_Records.HostId

WHERE t_Records.CheckDate IS NULL

OR t_Records.CheckDate < DATE_SUB(NOW(), INTERVAL t_Hosts.Frequency MINUTE); 
```

Enter fullscreen mode Exit fullscreen mode

这碰巧造成了更多的麻烦，所以现在又回到了日期时间。

另外，请确保为第二个 Id 添加别名，否则它可能会覆盖第一个表 Id！

编辑:这看起来不可行，实际上是有缺陷的。我的老逻辑(@mcfly 版)其实是该走的路，而不是五花肉愚蠢的左加入废话！

下面是 MySQL 版本。

```
SELECT t_Hosts.Id AS Id, MAX(Hostname) AS Hostname, MAX(Frequency) AS Frequency
FROM t_Hosts
LEFT JOIN t_Records
ON t_Hosts.Id = t_Records.HostId 
GROUP BY t_Hosts.Id
HAVING MAX(CheckDate) <= DATE_SUB(NOW(), INTERVAL MAX(Frequency) MINUTE)
UNION
SELECT Id, Hostname, Frequency
FROM t_Hosts
WHERE (
   Id NOT IN (
      SELECT HostId
      FROM t_Records
      WHERE (HostId IS NOT NULL)
   )
) 
```

Enter fullscreen mode Exit fullscreen mode