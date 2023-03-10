# 在 Postgres 中使用时间

> 原文：<https://dev.to/craigkerstiens/working-with-time-in-postgres>

*[原发于我在 craigkerstiens.com 的个人博客](http://www.craigkerstiens.com/2017/06/08/working-with-time-in-postgres/)*

大量的报告查询，无论是真正的密集型数据分析，还是涉及查看特定时间段内数据的基本业务洞察。Postgres 为开箱即用的时间处理提供了丰富的支持，这在处理数据库时通常是很不重要的。当然，如果您有一个时间序列数据库，这是不言而喻的，但是从查询的角度来看，它有多灵活和友好呢？有了 Postgres，你就可以使用很多关键项目，让我们深入挖掘那些让你的查询变得更容易的东西。

### 日期数学

我发现自己做的最常见的事情是查看在特定时间窗口内做了一些事情的用户。如果我从我的应用程序中执行这些，我可以很容易地注入具体的日期，但 Postgres 让这真的很容易。在 Postgres 中，有一种叫做区间的类型，它是一个时间窗口。幸运的是，Postgres 处理了如何将某个东西转换为小时/秒/毫秒等的繁重工作。以下是一些你可以利用间歇时间做的事情的例子:

*   1 天'::间隔
*   5 天'::间隔
*   1 周'::间隔
*   30 天'::间隔
*   1 个月'::间隔

*注意，如果你想去掉一整月，你实际上想用 1 个月，而不是自己计算。*

有了一个给定的时间间隔，你可以很容易地改变一些时间窗口，比如找到所有在过去一周内注册了你的服务的用户:

```
SELECT *
FROM users
WHERE created_at >= now() - '1 week'::interval 
```

Enter fullscreen mode Exit fullscreen mode

### 日期功能

日期数学让您很容易找到一些适用的特定数据集，但是当您想要一个更广泛的时间报告时，该怎么办呢？这里有几个选项。一个是利用内置的 Postgres 函数来帮助您处理日期和时间。`date_trunc`是最常用的将日期截断到某个间隔级别的工具之一。在这里，您可以使用与上面相同的一般值，但只需传入间隔的类型即可。因此，如果我们想知道每周注册的用户数:

```
SELECT date_trunc('week', created_at), 
       count(*)
FROM users
GROUP BY 1
ORDER BY 1 DESC; 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们一个每周有多少用户注册的很好的汇总。但是这里缺少的是如果你有一周没有用户。在这种情况下，因为没有用户注册，所以没有 0 计数，它只是简单地不存在。如果您确实想要这样的东西，您可以生成一些时间范围，然后用它与用户进行交叉连接，以查看他们属于哪一周。为此，首先要生成一系列日期:

```
SELECT generate_series('2017-01-01'::date, now()::date, '1 week'::interval) weeks 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将它与实际用户表连接，并检查`created_at`是否在正确的范围内。

```
with weeks as (
  select week
  from generate_series('2017-01-01'::date, now()::date, '1 week'::interval) week
)

SELECT weeks.week,
       count(*)
FROM weeks,
     users
WHERE users.created_at > weeks.week
  AND users.created_at <= (weeks.week - '1 week'::interval)
GROUP BY 1
ORDER BY 1 DESC; 
```

Enter fullscreen mode Exit fullscreen mode

### 时间戳与 Timestamptz

那么存储时间本身呢？Postgres 有两种类型的时间戳。它有一个通用时间戳和一个内嵌时区的时间戳。在大多数情况下，您通常应该选择 timestamptz。为什么不是时间戳？如果您移动了服务器，或者您的服务器以某种方式交换了它的配置，会发生什么呢？或者更实际一点，夏令时呢？总的来说，你可能认为你可以简单地输入你所看到的时间，但是当世界上不同的国家采用不同的夏令时，这就给你的应用程序带来了复杂性。

有了 timestamptz，当它进来的时候，它会知道你的时区的额外部分。当你从一个时区查询日光节约时间时，你就被覆盖了。有许多文章更深入地讨论了时间戳和带时区的时间戳之间的逻辑，所以如果你好奇，我鼓励你去看看，但是默认情况下，你只需要使用 timestamptz。

### 更

Postgres 在处理时间方面还有许多其他的功能和能力。您可以将时间设置或时间间隔的不同部分，如一天中的某个小时或一个月中的某个小时。你可以用`dow`抓取星期几。我最喜欢的是我们在 Citus 庆祝欢乐时光，UTC 00:00:00:00:00:00 的字面意思是 [`allballs()`](https://www.postgresql.org/message-id/20050124200645.GA6126%40winnie.fuhr.org) 。如果你需要在 Postgres 中处理日期和时间，我鼓励你在尝试重写你自己的东西之前查看一下[文档](https://www.postgresql.org/docs/current/static/functions-datetime.html)，你需要的东西可能已经在那里了。