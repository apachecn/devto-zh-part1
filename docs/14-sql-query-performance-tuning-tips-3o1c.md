# 14 个 SQL 查询性能调优技巧

> 原文：<https://dev.to/alexhyettdev/14-sql-query-performance-tuning-tips-3o1c>

我将以一个免责声明开始这篇文章。本质上，我是一个. NET 开发人员，而不是 SQL 专家，所以如果你有任何不同意的地方，我欢迎在评论中提出建设性的批评！希望这些 SQL 查询性能调优技巧仍然会有所帮助。

这些 SQL 提示和技巧来自去年优化 SQL 查询的经验。在某些情况下，这些变化将查询时间从几个小时缩短到几分钟甚至几秒钟。

SQL 优化是一门艺术。适用于一个服务器和数据集的方法不一定适用于另一个系统，所以请对下面的内容持保留态度，自己进行分析。

## 光标/循环

循环在任何程序员工具箱中都是一个稳定的结构。SQL 也可以执行循环，但是应该小心使用。英寸我们习惯于循环数据。一次处理一行数据还可以简化逻辑。

SQL 为这个过程提供了 While 循环和游标。然而，在我见过的大多数使用游标的情况下，它们可以被基于集合的查询和连接所取代。循环和游标对于小的一次性任务或批处理操作来说很好，但是在我看来，它们在高性能系统中没有用武之地。

如果你要做大量的更新或插入，我总是推荐使用循环(见下面的批处理)。

## Where 子句中的函数

有时，您需要在指定筛选条件之前转换数据。这有时是通过在 where 子句中使用函数来实现的。我最常见的地方是约会的时候。在某些情况下，你不关心事情发生的时间，只关心当天发生了什么。所以你会看到这样的东西:

```
SELECT *
FROM dbo.SomeTable
WHERE CONVERT(DATE, CreatedDate) = '2017-01-10' 
```

Enter fullscreen mode Exit fullscreen mode

这是一个问题。尤其是当你使用用户定义的函数时，因为 SQL 直到调用函数后才能解释这个值是什么。根据您的其他条件，这可能会导致 SQL 进行全表扫描。因此，在这些情况下，最好使用日期范围。

```
SELECT *
FROM dbo.SomeTable
WHERE CreatedDate >= '2017-01-10' AND CreatedDate < '2017-01-11' 
```

Enter fullscreen mode Exit fullscreen mode

这样做可以让 SQL 更容易地解释您的查询，从而使用正确的索引。注意，我使用了“2017-01-11”而不是“2017-01-10 23:59:59”，因为后者会错过一天的最后一秒。

## Where 子句中的逻辑

和我上面的观点一样徒劳。where 子句中的任何逻辑都需要删除。我经常在标准中的 CASE 语句中看到这种情况。如果 SQL 在理解您的标准之前必须做一些处理，那么它几乎肯定会做比它需要做的更多的工作。

```
SELECT *
FROM dbo.SomeTable
WHERE
CASE
   WHEN Number > 1000 THEN 'High'
   ELSE 'Low'
END = Threshold 
```

Enter fullscreen mode Exit fullscreen mode

上面是一个过于简化的例子，但是如果您发现您需要对数据进行一些转换以便能够查询它，那么这应该在一个临时表中完成。

```
CREATE TABLE #Temp
(
  Id BIGINT,
  Number BIGINT,
  Threshold VARCHAR(4)
)

INSERT INTO #Temp (Id, Number, Threshold)
SELECT
  Id,
  Number,
CASE WHEN Number > 1000 THEN 'High' ELSE 'Low' END AS Threshold
FROM dbo.SomeTable

SELECT *
FROM #Temp
WHERE Threshold = 'High'DROP TABLE #Temp 
```

Enter fullscreen mode Exit fullscreen mode

还是太简单了，但我希望你明白这一点。然后我会建议，如果数据足够大，可以在临时表上放一个索引。根据查询的不同，除了查询速度之外，还必须考虑创建索引的成本。毕竟，每次 SP 运行时，它都会这样做。

## 隐式数据转换

与上面的 3 个相比，这一个不会有太大的影响，但是每一毫秒都很重要！其中最常见的是在查询条件中指定 varchar 而不是 nvarchar。

但是，我见过用 varchar 查询 integer 列，用 integer 查询 varchar。

每次你这样做的时候，SQL 必须动态地转换值，而不是直接比较。这将使用更多的 CPU，并且会花费更长的时间。

```
SELECT *
FROM dbo.SomeFrom
WHERE Counter = ‘5' 
```

Enter fullscreen mode Exit fullscreen mode

您应该在哪里使用:

```
SELECT *
FROM dbo.SomeTable
WHERE Counter = 5 
```

Enter fullscreen mode Exit fullscreen mode

如果您查看实际查询计划提供的信息，它会告诉您这些隐式转换。

## 上下

在 C#中，进行比较时将字符串的大小写转换为大写和小写是很常见的。SQL 也有 upper 和 lower 函数，所以这种情况很常见:

```
SELECT *
FROM dbo.Currency
WHERE UPPER(CurrencySymbol) = 'GBP' 
```

Enter fullscreen mode Exit fullscreen mode

除了违反我的“标准中没有功能”的规则之外，它看起来确实是明智的。但是，SQL 有一个东西叫排序规则。默认情况下，英国的排序规则是 Latin1_General_CI_AS，美国的排序规则是 SQL _ Latin1 _ General _ CP1 _ CI _ A(source)。

这些名称中的 CI 代表不区分大小写。这意味着你可以这样做:

```
SELECT *
FROM dbo.Currency
WHERE CurrencySymbol = 'GBP' 
```

Enter fullscreen mode Exit fullscreen mode

或者甚至这样:

```
SELECT *
FROM dbo.Currency
WHERE CurrencySymbol = 'gBp' 
```

Enter fullscreen mode Exit fullscreen mode

并且仍然得到相同的结果，同时在这个过程中节省一些 CPU 周期。

## 临时表 vs 表变量

如果需要临时存储数据作为查询的一部分，有两种选择。

这样创建的临时表:

```
CREATE TABLE #TempTableName
(
   SomeId BIGINT,
   SomeOtherData varchar(50)
) 
```

Enter fullscreen mode Exit fullscreen mode

或者是这样创建的表格变量:

```
DECLARE @TempTableVariableName TABLE
(
   SomeId BIGINT,
   SomeOtherData varchar(50)
) 
```

Enter fullscreen mode Exit fullscreen mode

两者都可以用来存储数据，但是表变量实际上只能用于 1 或 2 行数据。这是因为 SQL 没有为表变量提供任何统计数据，并且您不能向它们添加索引来提高性能。

在某些情况下，比如函数，临时表不是一个选项，尽管如果你需要一个大表在你的函数中，它可能做得太多了。在这两种情况下，将大量数据放入临时表或表变量也不好，因为这可能会增大 tempDB。

如果您必须处理大量数据，有时最好将其放入一个临时表中进行处理。这只适用于批量报告，我仍然在寻找一种比实时报告中的临时表更好的方法。

## 配料数据

如果您需要在大型数据集中进行求和以及转换，您可能会尝试使用临时表。如果您的数据集是中等大小的，这没问题，但是如果您有超过一百万条记录要处理呢？您不希望在查询运行时将所有信息都存储在 temp DB 中。

您可以将转换后的数据插入到表中，但是如果您批量执行此操作，初始查询将在插入到表中之前再次存储在 temp DB 中。解决方案是分批进行转换，并将数据存储在临时表中。

```
DECLARE @Start DATETIME = '2016-01-01'
DECLARE @End DATETIME = '2017-01-01'
DECLARE @Next DATETIME = DATEADD(DAY,1,@Start)

WHILE (@Next <= @End)

BEGIN
   INSERT INTO dbo.SomeStagingTable (Id, Value1, Value2)
   SELECT Id, CASE WHEN IsFlag = 1 THEN 'Y' ELSE 'N' END, CreatedDate
   FROM dbo.SomeTable
   WHERE CreatedDate >= @Start AND CreatedDate < @Next

   SET @Start = @Next
   SET @Next = DATEADD(DAY,1,@Start)
END 
```

Enter fullscreen mode Exit fullscreen mode

通过按天对数据进行批处理，一次存储在 temp DB 中的数据量会更少，并且您通常会发现速度会更快。如果您有大量数据，请记住向临时表添加索引。

## 只选择你需要的

这在具有大量列的表上更为明显，但是只选择应用程序需要的内容是常识。如果您有一个 100 列的表，并且您的应用程序执行了一个`SELECT *`，那么将有大量的数据被检索并发送到您的应用程序。如果您的应用程序只使用 1 或 2 列，这将是对资源的巨大浪费。

您可能会发现，如果您只需要几个列，这些列就会被一个索引覆盖，从而节省了大量昂贵的键查找来获取您甚至不使用的数据。

## 避免使用相关的子查询

新的 SQL 开发人员有时会走捷径，使用子查询返回一些相关数据。

```
SELECT C.Name,
       C.Email,
       (SELECT CountryName FROM dbo.Country WHERE CountryId = C.CountryId)
FROM dbo.Customers C 
```

Enter fullscreen mode Exit fullscreen mode

这个查询的问题是它们倾向于逐行运行，因此性能很差。

这可以通过运行速度更快的左连接来完成。

```
SELECT C.Name,
              C.Email,
              CN.CountryName
FROM dbo.Customers C
LEFT JOIN dbo.Country CN ON CN.CountryId = C.CountryId 
```

Enter fullscreen mode Exit fullscreen mode

## 存在检查

这种糟糕的编码在 C#中也经常发生，并且在只有一点点有用的信息的时候却检索了太多的信息。

```
IF (SELECT COUNT(*) FROM dbo.Customers WHERE FirstName = @FirstName AND LastName = @LastName) > 0)
BEGIN
    PRINT ‘Customer exists'
END 
```

Enter fullscreen mode Exit fullscreen mode

当我们只需要查看一行是否存在时，为什么我们需要计算所有行。

SQL 有`EXISTS`用于此目的。那么这个怎么样:

```
IF EXISTS (SELECT * FROM dbo.Customers WHERE FirstName = @FirstName AND LastName = @LastName)
BEGIN
    PRINT 'Customer exists'
END 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里仍然掌握了太多的信息。SQL 可能很聪明，会忽略你已经做了`SELECT *`的事实，但是我不会给 SQL 这个选项。

我发现用于存在检查的最快的东西是`SELECT TOP 1 1`。如果存在，你将得到一个 1，如果不存在，你将得到一个`NULL`。我以前见过有人只做`SELECT 1`,但这将给你一个返回的每条记录 1。代码现在可以改为这样。

```
IF EXISTS (SELECT TOP 1 1 FROM dbo.Customers WHERE FirstName = @FirstName AND LastName = @LastName)
BEGIN
    PRINT 'Customer exists'
END 
```

Enter fullscreen mode Exit fullscreen mode

## 分析执行计划

如果不讨论 SQL 执行计划，一篇关于 SQL 性能的帖子是不完整的。要真正提高查询的性能，您需要理解和使用执行计划。我发现 [SentryOne Plan Explorer](https://sentryone.com/plan-explorer) 在理解这些计划和突出特定痛点方面非常有用。我不打算在这里详述，因为执行计划本身就保证了一个帖子。

执行计划非常有用，尤其是实际的执行计划。这些是您在阅读执行计划时需要特别注意的事情。

*   缺少索引–如果您的查询会受益于索引，那么 SQL 会告诉您。这显然会带来巨大的性能提升。
*   实际行数与估计行数–估计行数来自您正在使用的表上的 SQL 统计数据。如果这里有巨大的差异，那么这可能会影响性能，因为 SQL 可能没有使用正确的执行计划。我们需要反复试验，看看是否可以用不同的方式编写查询来改进这一点。
*   scan vs Seek–SQL 将告诉您执行了什么操作来检索信息。通常，您希望避免像瘟疫一样的全表扫描，在某些情况下，索引扫描需要通读整个表或索引，直到找到正确的值。理想情况下，您需要索引查找，尤其是聚集索引查找，因为这是最快的。如果您的索引不包含您需要的列，将执行键查找来检索数据。这些是无法避免的，但是如果需要的话，您可以添加一个索引，使用这些列来取消键查找。

我将在未来写一篇关于如何阅读执行计划的文章，因为有太多东西我没有在这里讨论。

## 指标

不用说，使用正确的索引可以极大地提高性能。同样，这可能需要一个帖子，而我还没有足够的知识来写它！

执行计划将告诉您缺少的索引，只要确保它们被添加到适当的位置。不过，在添加任何新索引之前，您需要咨询您的常驻 DBA。

## 动态 SQL

动态 SQL 似乎是一种福气。它使您能够创建更少的 sp，您可以根据自己的意愿从应用层进行调整。我确信动态 SQL 有一些很好的用途，但是一般来说应该非常小心地使用它(比如当你忘记了你妻子让你做的事情🙂 ).

动态 SQL 的问题是查询的执行计划不会被缓存。使用参数化的 SQL 更好也更安全(避免了 SQL 注入攻击)。为什么不通过 Dapper 或其他 ORM 来实现，并完全避免存储过程呢？

## 格式良好的代码

你知道吗，确保你的代码格式良好，缩进正确，实际上可以提高性能。

好吧，我是在开你的玩笑，但是请认真地帮你的开发伙伴一个忙，确保你的代码易于阅读。SQL 也是代码，没有什么比试图理解混乱的 SQL 代码更糟糕的了。

## 总结

我试图涵盖我所看到的导致我出现性能问题的主要方面。SQL 是一个有趣的东西，在一个环境中有效的东西，甚至一个查询在另一个环境中可能就无效了。因此，对这些查询进行性能测试总是很重要的，尤其是在高流量系统中。