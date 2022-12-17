# 在 MongoDB 中实现时间序列

> 原文：<https://dev.to/riccardo_cardin/implementing-time-series-in-mongodb>

*原贴于:[大泥球](http://rcardin.github.io/)T3】*

我最近来为 MongoDB 设计一个解决方案，以存储以时间为分析主轴的信息。这些信息应该以一种足够容易的方式存储，以便使用许多不同的时间粒度(月、周、天，...).信息还应该以一种不会消耗太多磁盘空间的方式存储，并且对于 MongoDB 的维护来说在性能上是最优的。总之，我需要在一个时间序列数据库中转换 MongoDB。

# 时间序列

让我们从头开始。什么是*时间序列*。引用[维基百科](https://en.wikipedia.org/wiki/Time_series):

> 时间序列是按时间顺序索引(或列出或绘制)的一系列数据点。最常见的是，时间序列是在连续的等间隔时间点取得的序列。因此，它是一个离散时间数据序列。

InfluxDB [关键概念页面](https://docs.influxdata.com/influxdb/v1.1/concepts/key_concepts/)给了我们一个非常简单的例子来理解什么是时间序列。想象一下，你有两个科学家，他们必须记录两个不同地点的两种昆虫的数量。使用表格视图，我们会遇到类似这样的情况。

```
time                  butterflies  honeybees  location  scientist
------------------------------------------------------------------
2015-08-18T00:00:00Z  12           23         1         langstroth
2015-08-18T00:00:00Z  1            30         1         perpetua
2015-08-18T00:06:00Z  11           28         1         langstroth
2015-08-18T00:06:00Z  3            28         1         perpetua
2015-08-18T05:54:00Z  2            11         2         langstroth
2015-08-18T06:00:00Z  1            10         2         langstroth
2015-08-18T06:06:00Z  8            23         2         perpetua
2015-08-18T06:12:00Z  7            22         2         perpetua 
```

如您所见，这个数据集市的主要维度是时间。存储在其中的事件跨越时间。在我们的例子中，我们称之为*的基本时间粒度*被设置为分钟。这意味着我们无法对这些数据进行粒度小于基本粒度(分钟)的分析。

像`butterflies`和`honeybees`这样的列称为*字段*。字段由字段键和字段值组成。*字段关键字* ( `butterflies`和`honeybees`)是字符串，存储*元数据*；字段关键字`butterflies`告诉我们字段值`12` - `7`指的是蝴蝶，字段关键字`honeybees`告诉我们字段值`23` - `22`指的是蜜蜂。

*字段值*是数据；时间序列数据库中的字段值总是与时间戳相关联。字段-键和字段-值对的集合组成了一个*字段集*。

最后，但同样重要的是，像`location`和`scientist`这样的列被称为*标签*。同样在这种情况下，标签由一个*标签键*和一个*标签值*组成。我们可以看看像*索引*这样的标签，它们有助于访问时间序列。它们不是强制性的，但是它们很有帮助。

我们现在有了时间序列。但是，这些数据库仅仅是一堆强制存在时间戳的表吗？不，时间序列数据库的主要特征是它应该有**强大的工具**来随着时间聚合数据(*字段*)。比方说，如果我们需要知道去年科学家“perpetua”在“位置 1”统计了多少只蝴蝶，那么从数据库中检索这些信息应该很容易。

# MongoDB 中的时间序列

MongoDB 是一个面向文档的通用数据库。这意味着数据库中的信息存储为文档。MongoDB 使用 BSON 格式，这是 JSON 文档的二进制变体。MongoDB 中的一个文档如下所示。

```
{
  _id: "yuebf09e-e0ewrewr-wererwer-324324edd",
  name: "Riccardo",
  surname: "Cardin",
  hobbies: ["computer science", "karate", "bass guitar"]
  //...
} 
```

在可用于字段值的类型中，我们找到了*字符串*、*数字*、*时间戳*、*数组*、*文档*等等。如何设计一个文档模式，让 MongoDB 可以管理时间序列？

出于本文稍后将分析的许多原因，在 MongoDB 中处理时间序列的最佳方式是为我们想要管理的每个聚合级别使用一个*子文档*。让我们把上面这个关于科学家和昆虫的例子转换成 MongoDB。

假设您希望将分钟作为一段时间内的最大粒度。然后，假设您还想访问以小时、天、月为单位的部分聚合信息。您应该使用的最终最佳文档模式如下。

```
{
  _id: "1234567890",
  // The document is indexed by month
  date: ISODate("2015-08-01T00:00:00.000Z"),
  // In the main document we fing tags
  location: 1,
  scientist: "langstroth",
  days: [
    // We have a subdocument in the days array for every day in the month
    {
      date: ISODate("2015-08-18T00:00:00.000Z"),
      // In each subdocument we find field keys and values
      butterflies: 23,
      honeybees: 51,
      hours: [
        // We have a subdocument for every hour in a day
        {
          date: ISODate("2015-08-18T00:00:00.000Z"),
          butterflies: 23,
          honeybees: 51,
          minutes: [
            {
              // And finally, we find the minutes granularity. Also here
              // we have a subdocument for each minute in an hour
              date: ISODate("2015-08-18T00:00:00.000Z"),
              butterflies: 12,
              honeybees: 23
            },
            // Omissis...
            {
              date: ISODate("2015-08-18T00:06:00.000Z"),
              butterflies: 11,
              honeybees: 28
            },
            // Omissis...
          ]
        },
        // Omissis...
      ]
    },
    // Omissis...
  ]
} 
```

这么大的文件，不是吗？如您所见，这里的技巧是为我们分析中所需的粒度设置一个子文档级别。标签在主文档中，比如说在第 0 层。字段在每个级别(1，2，...).一段时间内的聚合由每个级别的`date`属性值决定。文件总是*完整*。这意味着我们将为每一分钟/小时/天找到一个子文档，不管字段值是否为 0。

## 这是为什么？为什么会这样？

到目前为止一切顺利。现在，问题是:我们为什么要使用这种复杂的文档模式？有哪些利弊？

首先，如果我们对文档使用 1:1 的方法来建模我们的事件，我们最终会为每个事件生成一个文档。

```
{
  _id : "1234567890"
  date: ISODate("2015-08-18T00:00:00.000Z"),
  location: 1
  scientist: "langstroth"
  butterflies: 12,
  honeybees: 23
},
{
  _id : "0987654321"
  date: ISODate("2015-08-18T00:06:00.000Z"),
  location: 1
  scientist: "langstroth"
  butterflies: 11,
  honeybees: 28
} 
```

虽然这种方法在 MongoDB 中是有效的，但是它没有利用文档模型的表达特性。此外，为了使用子文档方法聚合一段时间内的结果，MongoDB 只需要访问很少的文档。

另一个好问题是，为什么我们使用数组来表示日/小时和分钟信息，而不是为每个元素使用专用的 JSON 属性。Arrays 与 [MongoDB 聚合框架](https://docs.mongodb.com/manual/aggregation/)配合得非常好。详细地说，使用 [`$unwind`](https://docs.mongodb.com/manual/reference/operator/aggregation/unwind/) 操作符，可以使每个文档的内部结构变得扁平，从而使查询存储在子文档中的信息的过程变得简单。

例如，使用以下聚合管道可以轻松检索到科学家 *langstroth* 在 2015 年 8 月 18 日至 2015 年 8 月 20 日期间在地点 1 报告的蝴蝶数量。

```
db.test.aggregate([
    {$match: {location: 1, scientist: 'langstroth'}},
    {$unwind: '$days'},
    {$match: {'$and': [{'days.date': {'$gte' : ISODate("2015-08-18T00:00:00.000Z")}},
                       {'days.date': {'$lte' : ISODate("2015-08-20T00:00:00.000Z")}}]}},
    {$project: {_id : 0, location: 1, scientist: 1, 'butterflies' : '$days.butterflies'}},
    {$group: {_id : {location: '$location', scientist: '$scientist'}, butterflies: {'$sum': '$butterflies'}}}
]) 
```

在这个例子中，我们使用子文档的级别，即天、小时和分钟。显然，所有这些级别都不是强制性的。但是，通过这种方式，我们可以在更新文档时提高更新性能。任何数组实际上都更小，因为它只包含全部信息的一小部分。然后，MongoDB 可以在更新过程中更快地遍历数组。

另一件重要的事情是，主文档必须以完整的形式插入到集合中，这意味着已经填充了所有的粒度级别。显然，在开始时，每个子文档中的所有字段值都等于零。然而，这是一个需要考虑的重要要求。通过这种方式，*没有更新会导致现有文档在磁盘*上增长或移动。这个事实允许 MongoDB 在集合上有更好的表现。

但是，这个需求提出了一个关于使用 MongoDB 管理时间序列的重要问题:谁负责为集合中的每个*标签集*插入“全零”文档？

## 先有鸡还是先有蛋？

这是使用 MongoDB 对时间序列建模的真正核心问题。我们需要一个在使用和更新文档之前插入文档的过程。

第一次尝试:我们可以开发一个过程，定期为我们插入那些文档。干得好，伙计。然而，这种方法对于那些标签的*域先验*未知*的用例是不可能的。回到
的例子，想象你的系统正在从全世界的科学家那里收集蝴蝶和蜜蜂的数量。知道所有这些科学家的名字是不切实际的。*

第二次尝试:尝试在一个`$update` ( *upsert* )语句中使用 [`$setOnInsert`](https://docs.mongodb.com/manual/reference/operator/update/setOnInsert/)
子句来获得一些优势。从 MongoDB 文档中，我们有:

> 如果带有 upsert: true 的更新操作导致文档的插入，那么$setOnInsert 将指定的值分配给文档中的字段。如果更新操作没有导致插入，`$setOnInsert`不做任何事情。

答对了。我们找到了！第一次尝试更新集合时，我们可以将整个文档插入到一个`$setOnInsert`子句中。*没有*。由于[中解释的一个错误，这个堆栈溢出问题](https://stackoverflow.com/questions/41552405/mongodb-collection-update-initialize-a-document-with-default-values)不可能在`$setOnInsert`和`$update`子句中使用相同的属性。S**t！

# 三步初始化

那么，我们是不是走进了一个死胡同，一个死胡同？乍一看，似乎是这样。幸运的是，我和我的同事找到了一个*的变通办法*。我们可以称之为*三步初始化*。简单回顾一下，我们希望能够初始化主
文档，并正确设置所有必需的子文档。

请记住，MongoDB 保证了单个文档操作的原子性。考虑到这一事实，我们可以按以下方式操作:

1.  尝试更新文档，在指定的时间段适当地增加计数器。不做任何*更新*，只是一个老式的[更新](https://docs.mongodb.com/manual/reference/method/db.collection.update/)操作。请记住，update 语句的执行会返回所写文档的数量。如果编写的文档数大于零，就大功告成了。
2.  如果更新写入的文档数为零，则意味着集合中还没有要更新的相关文档。尝试为指定的*标签*将整个文档[插入](https://docs.mongodb.com/manual/reference/method/db.collection.insert/)。将所有计数器(*字段值*)归零。insert 语句的执行还会返回写入的文档数。如果它返回零或抛出一个异常，没关系:这意味着一些其他进程已经为相同的标签插入了文档。
3.  再次执行上述更新。

代码应该类似于下面的代码片段。在这里，我们希望将日期为 2015-08-01T00:06:00.000Z 的蝴蝶和蜜蜂的字段值加 1，并将*标记为*位置 1 和科学家“langstroth”。

```
// Firt of all, try the update
var result = db.test.update(
    {date: ISODate("2015-08-01T00:00:00.000Z"), location: 1, scientist: "langstroth"},
    {$inc :{
              butterflies: 1,
              honeybees: 1,
              "days.0.butterflies": 1,
              "days.0.honeybees": 1,
              "days.0.hours.0.butterflies": 1,
              "days.0.hours.0.honeybess": 1,
              "days.0.hours.0.minutes.6.butterflies": 1,
              "days.0.hours.0.minutes.6.honeybess": 1,
            }
    },
    {upsert: false}
);
// If the update do not succeed, then try to insert the document
if (result.nModified === 0) {
    try {
        db.test.insert(/* Put here the whole document */);
    } catch (err) {
        console.log(err);
    }
    // Here we are sure that the document exists.
    // Retry to execute the update statement
    db.test.update(/* Same update as above */);
} 
```

显然，使上述过程起作用的是对文档修改的原子性的保证。我知道，这个过程有点令人毛骨悚然，但我们目前没有找到更好的方法。你有更好的主意吗？如果有，试着在评论区解释一下。谢谢！

# 参考文献

*   [时间序列](https://en.wikipedia.org/wiki/Time_series)
*   [InfluxDB 关键概念](https://docs.influxdata.com/influxdb/v1.2/concepts/key_concepts/)
*   [MongoDB 中时序数据的模式设计](https://www.mongodb.com/blog/post/schema-design-for-time-series-data-in-mongodb)
*   [用于时序数据的 MongoDB 第 1 部分:为传感器管理做准备](https://www.mongodb.com/presentations/mongodb-time-series-data-part-1-setting-stage-sensor-management)
*   [用于时序数据的 MongoDB 第 2 部分:使用聚合框架和 Hadoop 分析时序数据](https://www.mongodb.com/presentations/mongodb-time-series-data-part-1-setting-stage-sensor-management)
*   [MongoDB 聚合框架](https://docs.mongodb.com/manual/aggregation/)
*   [MongoDB 集合更新:用默认值初始化文档](https://stackoverflow.com/questions/41552405/mongodb-collection-update-initialize-a-document-with-default-values)