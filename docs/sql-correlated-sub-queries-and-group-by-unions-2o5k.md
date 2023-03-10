# SQL 关联子查询和按联合分组

> 原文：<https://dev.to/adamkdean/sql-correlated-sub-queries-and-group-by-unions-2o5k>

在进行新的正常运行时间和延迟监控项目时，我遇到了比平常更多的 SQL 难题，通过有比平常更高的需求，我学到了很多。要称自己为程序员，我觉得你至少要有基本的 SQL 技能。它们是普通人的基本数学技能，每个程序员都应该理解选择、插入、更新和删除..很长一段时间我都是他们中的一员，我讨厌 SQL，因为我不理解它，或者不想理解它..但是需要它，并且需要它来做高级的事情，意味着您需要学习，并且当您可以通过卸载到 SQL 上来削减如此多的编码时，它确实节省了您的时间！

因此，我现在正在做的这一部分得到了一个主机列表，这些主机的最后一条记录(延迟检查)超过了频率阈值。当前的 SQL 很棒，它从一个角度检查最后的日期是否超过了阈值，但是我意识到一个小问题..它不会得到零记录的新主机。它将只适用于以前检查过的主机！

```
SELECT HostId, MAX(Hostname) AS Hostname, MAX(CheckDate) AS CheckDate, MAX(Frequency) AS Frequency
FROM vw_HostRecords
GROUP BY HostId
HAVING (DATEADD(minute, MAX(Frequency), MAX(CheckDate)) < GETDATE()) 
```

Enter fullscreen mode Exit fullscreen mode

所以我不得不再次改进我的 SQL，增加相关的子查询。这其实很简单，只是听起来很聪明，但是嘘..下面是检查记录中是否存在行的 SQL，如果不存在，它从主机中选择该行。基本上它为我得到所有新的。

```
SELECT Id AS HostId, Hostname, Frequency
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

因此，现在我需要使用一个 union 来混合这些，并添加 CheckDate = NULL，以便 UNION 能够工作(要使用它，两个选择必须具有相同的字段/列)..我们最终得到了这个可爱的作品:

```
SELECT HostId, MAX(Hostname) AS Hostname, MAX(Frequency) AS Frequency, MAX(CheckDate) AS CheckDate
FROM vw_HostRecords
GROUP BY HostId
HAVING (DATEADD(minute, MAX(Frequency), MAX(CheckDate)) < GETDATE())
UNION
SELECT Id AS HostId, Hostname, Frequency, NULL AS CheckDate
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

太棒了，不是吗？

编辑:不得不修复一个错误，似乎你也必须以正确的顺序获得所有字段，因为 SQL 不会为你混合和匹配！

所以:从 table_a union 中选择 a，b，c 从 union_b 中选择 a，b，c 等等！

编辑 2:删除或从有线