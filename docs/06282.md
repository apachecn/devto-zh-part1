# MongoDB 3.4 中的只读视图

> 原文：<https://dev.to/damcosset/read-only-views-in-mongodb-34>

## 简介

MongoDB 在 3.4 版本中引入了只读视图。本文将简要探讨它们是什么，如何使用它们，以及为什么要使用它们。

## 什么？

只读视图是合成集合。除了它们不包含任何文档这一事实之外，从阅读它们的角度来看，它们很像一个真正的集合。视图的状态依赖于一个真实的集合，即源集合。每当您查询一个只读视图时，您实际上是聚合了源集合。不能写视图(那是只读部分！).如果您更新源集合，它将传播到您的视图(取决于您如何定义您的视图)。除了创建一个新文档之外(稍后我们将看到)，视图不占用任何空间。

## 如何？

在这一点上，举个例子可能更有用。我将使用 mongo shell 来演示一个只读视图。这是我现在的数据库:

```
> use team
switched to db team

> db.players.find().pretty()
{
        "_id" : ObjectId("596b44d46879c5e83035f228"),
        "firstName" : "Joe",
        "lastName" : "Johnson",
        "position" : "forward",
        "number" : 9,
        "isMarried" : true
}
{
        "_id" : ObjectId("596b44d46879c5e83035f229"),
        "firstName" : "Alex",
        "lastName" : "Jones",
        "position" : "goalkeeper",
        "number" : 16,
        "isMarried" : false
}
{
        "_id" : ObjectId("596b44d46879c5e83035f22a"),
        "firstName" : "Ethan",
        "lastName" : "Hawk",
        "position" : "defender",
        "number" : 4,
        "isMarried" : false
}
{
        "_id" : ObjectId("596b44d46879c5e83035f22b"),
        "firstName" : "Richard",
        "lastName" : "Dawkins",
        "position" : "midfielder",
        "number" : 8,
        "isMarried" : true
} 
```

Enter fullscreen mode Exit fullscreen mode

我的玩家收藏里有一堆文档。在每个文档中都定义了名、姓、位置、号码和婚姻。接下来，我将使用 *db.createView()* 创建一个只读视图。它有 3 个参数:第一个是视图的名称，第二个是源集合，第三个是包含聚合管道的数组。这是您定义视图如何操作源集合的地方。

```
> db.createView("playersInfos", "players", [ {$project : { "firstName": 1, "lastName": 1, "position": 1} } ] )
{ "ok" : 1 }

> show collections
players
playersInfos
system.views

> db.system.views.find()
{ "_id" : "team.playersInfos", "viewOn" : "players", "pipeline" : [ { "$project" : { "firstName" : 1, "lastName" : 1, "position" : 1 } } ] } 
```

Enter fullscreen mode Exit fullscreen mode

我将我的视图命名为*player infos*。来源收集是*玩家*。在第三个参数中，我只使用了 *$project* 来返回名字、姓氏和职位字段。当我在我的数据库中查找收藏时，我可以看到我的*player infos*和 *system.views* 已被添加。 *system.views* 跟踪您的视图。现在，让我们质疑我们的观点。你这样做就像你做任何其他收藏:

```
> db.playersInfos.find()
{ "_id" : ObjectId("596b44d46879c5e83035f228"), "firstName" : "Joe", "lastName" : "Johnson", "position" : "forward" }
{ "_id" : ObjectId("596b44d46879c5e83035f229"), "firstName" : "Alex", "lastName" : "Jones", "position" : "goalkeeper" }
{ "_id" : ObjectId("596b44d46879c5e83035f22a"), "firstName" : "Ethan", "lastName" : "Hawk", "position" : "defender" }
{ "_id" : ObjectId("596b44d46879c5e83035f22b"), "firstName" : "Richard", "lastName" : "Dawkins", "position" : "midfielder" } 
```

Enter fullscreen mode Exit fullscreen mode

当我创建视图时，我只得到在我的聚合管道中定义的内容。

现在，我忘了我队里的一个队员。我将向我的*玩家*集合添加一个新文档，并再次查询我的视图。请记住，您不能写入只读视图。

```
> db.players.insertOne({"firstName": "Rigoberto", "lastName": "Song", "position": "wingback", "number": 2, "isMarried": false})
{
        "acknowledged" : true,
        "insertedId" : ObjectId("596b47506879c5e83035f22c")
}
> db.playersInfos.find()
{ "_id" : ObjectId("596b44d46879c5e83035f228"), "firstName" : "Joe", "lastName" : "Johnson", "position" : "forward" }
{ "_id" : ObjectId("596b44d46879c5e83035f229"), "firstName" : "Alex", "lastName" : "Jones", "position" : "goalkeeper" }
{ "_id" : ObjectId("596b44d46879c5e83035f22a"), "firstName" : "Ethan", "lastName" : "Hawk", "position" : "defender" }
{ "_id" : ObjectId("596b44d46879c5e83035f22b"), "firstName" : "Richard", "lastName" : "Dawkins", "position" : "midfielder" }
{ "_id" : ObjectId("596b47506879c5e83035f22c"), "firstName" : "Rigoberto", "lastName" : "Song", "position" : "wingback" } 
```

Enter fullscreen mode Exit fullscreen mode

文档已经被插入到源集合中，我的视图能够立即查询它。

太好了！现在让我们把事情复杂化一点。我将删除我的视图并重新创建它。我将修改聚合器管道。

```
> db.playersInfos.drop()
true

> db.createView("playersInfos", "players", [{$project : { "fullName": {$concat: ["$firstName", " ", "$lastName"]}, "position": 1}}])
{ "ok" : 1 }

> db.playersInfos.find()
{ "_id" : ObjectId("596b44d46879c5e83035f228"), "position" : "forward", "fullName" : "Joe Johnson" }
{ "_id" : ObjectId("596b44d46879c5e83035f229"), "position" : "goalkeeper", "fullName" : "Alex Jones" }
{ "_id" : ObjectId("596b44d46879c5e83035f22a"), "position" : "defender", "fullName" : "Ethan Hawk" }
{ "_id" : ObjectId("596b44d46879c5e83035f22b"), "position" : "midfielder", "fullName" : "Richard Dawkins" }
{ "_id" : ObjectId("596b47506879c5e83035f22c"), "position" : "wingback", "fullName" : "Rigoberto Song" } 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我不希望名字和姓氏出现在不同的字段中。我只想要一个字段全名。我在第三个参数中使用了 *$concat* 来创建它。

## 为什么？

为什么要使用视图？毕竟，我们已经可以通过常规收集完成所有这些工作。两个原因:

*   安全性

您可以允许应用程序在不公开某些字段的情况下查询视图。如果您在您的集合中存储了敏感信息，即使这些信息在源集合中，如果定义正确，也不能从视图中访问这些信息。

*   简单

如果您发现自己在一遍又一遍地查询相同的信息，那么您可以定义一个视图，让您不必在每次需要查询集合时都指定查询标准。

## 警告

不能用 *MapReduce* ， *$text* ， *$* ， *$slice* ， *$elemMatch* ， *$meta* 带视图。