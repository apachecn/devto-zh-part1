# 阅读 Postgres 解释分析查询计划

> 原文：<https://dev.to/calebhearth/reading-a-postgres-explain-analyze-query-plan-3gb8>

我们用来理解和优化 SQL 查询的最强大的工具是 [`EXPLAIN ANALYZE`](https://www.postgresql.org/docs/9.5/static/sql-explain.html) ，这是一个 Postgres 命令，它接受诸如`SELECT ...`、`UPDATE ...`或`DELETE ...`之类的语句，执行该语句，而不是返回数据，而是提供一个查询计划，详细说明计划器采取什么方法来执行所提供的语句。

下面是使用解释页面 :
从 [Postgres 中提取的一个查询](https://www.postgresql.org/docs/9.5/static/using-explain.html)

```
EXPLAIN ANALYZE SELECT *
FROM tenk1 t1, tenk2 t2
WHERE t1.unique1 < 100 AND t1.unique2 = t2.unique2 ORDER BY t1.fivethous;

                                                                 QUERY PLAN
--------------------------------------------------------------------------------------------------------------------------------------------
 Sort (cost=717.34..717.59 rows=101 width=488) (actual time=7.761..7.774 rows=100 loops=1)
   Sort Key: t1.fivethous
   Sort Method: quicksort Memory: 77kB
   -> Hash Join (cost=230.47..713.98 rows=101 width=488) (actual time=0.711..7.427 rows=100 loops=1)
         Hash Cond: (t2.unique2 = t1.unique2)
         -> Seq Scan on tenk2 t2 (cost=0.00..445.00 rows=10000 width=244) (actual time=0.007..2.583 rows=10000 loops=1)
         -> Hash (cost=229.20..229.20 rows=101 width=244) (actual time=0.659..0.659 rows=100 loops=1)
               Buckets: 1024 Batches: 1 Memory Usage: 28kB
               -> Bitmap Heap Scan on tenk1 t1 (cost=5.07..229.20 rows=101 width=244) (actual time=0.080..0.526 rows=100 loops=1)
                     Recheck Cond: (unique1 < 100)
                     -> Bitmap Index Scan on tenk1_unique1 (cost=0.00..5.04 rows=101 width=0) (actual time=0.049..0.049 rows=100 loops=1)
                           Index Cond: (unique1 < 100)
 Planning time: 0.194 ms
 Execution time: 8.008 ms 
```

Enter fullscreen mode Exit fullscreen mode

Postgres 构建了一个计划节点的树形结构，代表所采取的不同行动，根和每个`->`都指向其中一个。在某些情况下，`EXPLAIN ANALYZE`提供了执行次数和行数之外的额外的执行统计信息，比如上面的`Sort`和`Hash`。除了第一行以外没有`->`的任何一行都是这样的信息，所以查询的结构是:

```
Sort
└── Hash Join
    ├── Seq Scan
    └── Hash
        └── Bitmap Heap Scan
            └── Bitmap Index Scan 
```

Enter fullscreen mode Exit fullscreen mode

每棵树的分支代表子操作，您将从内向外工作以确定“首先”发生了什么(尽管同一级别的节点顺序可能不同)。

做的第一件事是在`tenk_unique1`指标上做一个`Bitmap Index Scan`:

```
-> Bitmap Index Scan on tenk1_unique1 (cost=0.00..5.04 rows=101 width=0) (actual time=0.049..0.049 rows=100 loops=1)
      Index Cond: (unique1 < 100) 
```

Enter fullscreen mode Exit fullscreen mode

这对应于 SQL `WHERE t1.unique1 < 100`。Postgres 正在查找匹配索引条件`unique1 < 100`的行的位置。行本身不会被返回到这里。成本估算`(cost=0.00..5.04
rows=101 width=0)`意味着 Postgres 预计它将“花费”5.04 个任意计算单位来找到这些值。0.00 是该节点可以开始工作的开销(在这种情况下，只是查询的启动时间)。`rows`是索引扫描将返回的估计行数，`width`是返回行的估计大小(0，因为我们只关心位置，而不关心行的内容)。

因为我们使用`ANALYZE`选项运行了`EXPLAIN`，所以实际上执行了查询并捕获了计时信息。`(actual time=0.049..0.049
rows=100 loops=1)`表示索引扫描执行了 1 次(`loops`值)，返回了 100 行，实际时间为 0 毫秒。在一个节点执行多次的情况下，实际时间是每次迭代的平均值，您可以将该值乘以循环次数来获得实际时间。范围值也可能不同，这给出了花费的最小/最大时间的概念。这为该查询建立了每个成本单位 0.049 毫秒/ 5.04 单位≈0.01 毫秒/单位的成本比率。

索引扫描的结果被传递给`Bitmap Heap Scan`动作。在这个节点中，Postgres 获取 tenk1 表中行的位置，别名为 t1，其中`unique1 < 100`并从表本身获取行。

```
-> Bitmap Heap Scan on tenk1 t1 (cost=5.07..229.20 rows=101 width=244) (actual time=0.080..0.526 rows=100 loops=1)
      Recheck Cond: (unique1 < 100) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，当成本期望值乘以我们计算的值 0.01 时，将意味着粗略的预期时间为(229.20-5.07)* 0.01≈2.24 毫秒，我们看到每行的实际时间为 0.526 毫秒，相差 4 倍。这可能是因为成本估计是一个上限，并不是所有的行都需要被读取，或者因为重新检查条件总是真的。

`Bitmap Index Scan`和`Bitmap Heap Scan`的组合比从表中顺序读取行的开销要大得多(一个`Seq Scan`)，但是因为在这种情况下需要访问的行相对较少，所以两步过程最终会更快。通过在获取行之前按物理顺序对它们进行排序，可以进一步加快速度，这样可以最小化单独获取的成本。节点名中的“位图”进行排序。

堆扫描的结果，即 tenk1 中那些`unique1 < 100`为真的行，在被读取时被插入到内存哈希表中。从成本上我们可以看出，这根本不需要时间。

```
-> Hash (cost=229.20..229.20 rows=101 width=244) (actual time=0.659..0.659 rows=100 loops=1)
      Buckets: 1024 Batches: 1 Memory Usage: 28kB 
```

Enter fullscreen mode Exit fullscreen mode

哈希节点包括关于哈希桶和批处理的数量以及峰值内存使用的信息。如果 Batches > 1，还会涉及到磁盘使用，但不会显示出来。内存使用在 100 行* 244 字节= 24.4 kB 时是有意义的，这足够接近我们的目的的 28kB，我们可以假设它是哈希键本身占用的内存。

接下来，Postgres 从 tenk2(别名为 t2)中读取所有 10000 行，并根据 tenk1 行的散列对它们进行检查。散列连接意味着一个表的行被输入到内存中的散列中(到目前为止我们已经构建了这个散列),然后扫描另一个表的行，并根据哈希表探测它的值以寻找匹配。我们在第二行看到“匹配”的条件，`Hash
Cond: (t2.unique2 = t1.unique2)`。请注意，因为查询选择了 tenk1 和 tenk2 中的所有值，所以在散列连接期间，每一行的宽度都会加倍。

```
-> Hash Join (cost=230.47..713.98 rows=101 width=488) (actual time=0.711..7.427 rows=100 loops=1)
      Hash Cond: (t2.unique2 = t1.unique2)
      -> Seq Scan on tenk2 t2 (cost=0.00..445.00 rows=10000 width=244) (actual time=0.007..2.583 rows=10000 loops=1) 
```

Enter fullscreen mode Exit fullscreen mode

既然已经收集了满足我们条件的所有行，我们可以通过`Sort Key: t1.fivethous`对结果集进行排序。

```
Sort (cost=717.34..717.59 rows=101 width=488) (actual time=7.761..7.774 rows=100 loops=1)
  Sort Key: t1.fivethous
  Sort Method: quicksort Memory: 77kB 
```

Enter fullscreen mode Exit fullscreen mode

排序节点包括有关用于排序的算法、`quicksort`、排序是在内存中还是在磁盘上完成(这极大地影响了速度)以及所需的内存/磁盘空间量的信息。

了解如何阅读查询计划对于优化查询非常重要。例如，Seq Scan 节点通常表示添加索引的机会，这样读取速度会快得多。熟悉这些计划将使你成为一名更好的数据库工程师。有关查询计划的更多示例，请阅读本示例中的 [Using `EXPLAIN`](https://www.postgresql.org/docs/9.5/static/using-explain.html) 。