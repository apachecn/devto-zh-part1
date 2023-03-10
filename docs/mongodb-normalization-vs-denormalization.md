# MongoDB:规范化与反规范化

> 原文：<https://dev.to/damcosset/mongodb-normalization-vs-denormalization>

## 简介

规划应用程序的一个重要步骤是如何表示数据。您将如何组织数据库的集合和文档？设计不良的数据表示会迫使您进行不必要的查询来获取所需的数据，从而降低应用程序的速度。

## 什么是正常化？

当您规范化您的数据时，您将您的数据划分为多个集合，这些集合之间有引用。每条数据都在一个集合中，但是多个文档会引用它。这意味着，要更改数据，只需更新一个文档，因为该数据只定义一次。然而，MongoDB 不像 SQL 那样有任何连接功能。因此，如果您需要来自几个集合的数据，您将需要执行几个查询。

让我们看一个正常化的例子。我们有一个用户集合。我们将每个用户的偏好存储在 accountsPref 集合中。我们将用户写的每篇文章存储在一个文章集合中。

在一个规范化的例子中，我们的集合可能是这样的:

```
db.users.findOne({_id: userId})
{
  _id: ObjectId("5977aad83abbae8aef44b47b"),
  name: "John Doe",
  email: "johndoe@gmail.com",
  articles: [
    ObjectId("5977aad83abbae8aef44b47a"),
    ObjectId("5977aad83abbae8aef44b478"),
    ObjectId("5977aad83abbae8aef44b477")
  ],
  accountsPref: ObjectId("5977aad83abbae8aef44b476")
}

db.accountsPref.findOne({_id: id})
{
  _id: ObjectId("5977aad83abbae8aef44b490"),
  userId: ObjectId("5977aad83abbae8aef44b47b"),
  showFriends: true,
  notificationsOne: false,
  style: "light"
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在集合中存储每条数据的引用。这是在关系数据库中经常使用的方法。但是，在 MongoDB 中，您可能不希望以这种方式存储数据。它需要大量的查询来获得你想要的数据。要获得一个用户的信息，你需要对数据库进行三次访问。一个用于用户，一个用于 accountsPref，另一个用于文章。哦...

## 反规格化

你想优化你的阅读。你不想三趟数据库来获取你的信息。我们可以将每个用户的账户偏好存储为一个嵌入式文档，就像这样:

```
{
  _id: ObjectId("5977aad83abbae8aef44b47b"),
  name: "John Doe",
  email: "johndoe@gmail.com",
  articles: [
    ObjectId("5977aad83abbae8aef44b47a"),
    ObjectId("5977aad83abbae8aef44b478"),
    ObjectId("5977aad83abbae8aef44b477")
  ],
  accountsPref: {
    style: "light",
    showFriends: true,
    notificationsOn: false
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样做的好处是，您需要更少的查询来获取信息。缺点是占用空间更大，更难保持同步。例如，我们决定将*光*样式重命名为*日*。我们必须更新 user.accountsPref.style 为 *light* 的每一个文档。

你可以混合使用引用和嵌入。您可以保留子文档，添加您的帐户首选项的引用，但只将最常用的字段放在子文档中。如果你知道只有*样式*字段被你的应用频繁使用，你可以这样做:

```
{
  _id: ObjectId("5977aad83abbae8aef44b47b"),
  name: "John Doe",
  email: "johndoe@gmail.com",
  articles: [
    ObjectId("5977aad83abbae8aef44b47a"),
    ObjectId("5977aad83abbae8aef44b478"),
    ObjectId("5977aad83abbae8aef44b477")
  ],
  accountsPref: {
    _id: ObjectId("5977aad83abbae8aef44b490"),
    style: "light"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能是一个不错的方法，因为您的需求可能会随着时间而变化。如果您想要在页面上包含更多或更少的信息，您可以随时在嵌入的文档中添加或删除字段。

## 注意事项

知道如何设计你的数据总是很复杂。一个重要的考虑因素是数据更改的频率与读取数据的频率。标准化将提供更新高效的数据表示。反规格化将使数据读取高效。

您是否应该在每个用户的文档中存储帐户偏好设置？大体上是的。它只与这个用户相关。大概也不会有太大变化。

在文档中保存用户的地址可能也是一个好主意。它不会定期更新，所以你不应该因为这些信息的相关性而惩罚每一次阅读。

考虑这个问题时，以下是一些帮助您的指导原则:

### 嵌入:

*   您有小的子文档
*   您的数据不需要定期更改
*   你不需要即时的一致性(不是最新的)
*   您的文档会有少量增长
*   您需要这些数据来执行第二次查询
*   你想要更快的阅读速度

### 引用:

*   您有大型子文档
*   您的数据经常变化
*   你需要你的数据是最新的
*   您的文档会大量增长
*   您的数据经常被排除在结果之外
*   您想要更快的写入速度