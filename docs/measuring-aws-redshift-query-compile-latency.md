# 测量 AWS 红移查询编译延迟

> 原文：<https://dev.to/wrschneider/measuring-aws-redshift-query-compile-latency>

*本文最初[出现在我的博客](http://wrschneider.github.io/2017/06/02/redshift-compile-latency.html)上*

AWS 很清楚，Redshift 的分布式架构在每次发出新的查询时都会产生固定成本。文档称这种影响“在运行一次性(特别)查询时可能会特别明显。”

我进一步尝试量化“明显”的确切含义。

为了将数据缓存命中/未命中的影响从查询编译中隔离出来，我在空表上运行了一些查询，因此没有要加载或缓存的数据。通过更改列或聚合函数，对每个查询稍作修改以触发重新编译。

我发现编译延迟与查询的复杂性成比例。

*   简单查询:通常在 1-1.5 秒之间，异常值在 3 秒左右。简单查询的示例:

```
select sum(a1) from foo where a2 = 1;
select sum(a2) from foo where a3 = 1;
-- etc. 
```

*   具有更多条件和分组依据的更复杂的查询:通常大约 2-3 秒。此类别中的查询示例:

```
select a8, a9, sum(a1), sum(a2)
from foo
where foo.a3 > 10 and foo.a4 < foo.a5
group by a8, a9; 
```

*   更复杂的是，连接和分组:平均大约 5 秒，范围在 3-7 秒之间。示例查询:

```
select s, s2, count(a6), sum(a7)
from foo
join bar on bar.a = foo.a6
join baz on baz.b = foo.a7
where foo.a3 = 1 and baz.s2 is not null
group by s, s2; 
```