# MongoDB 聚合框架入门

> 原文：<https://dev.to/damcosset/getting-started-with-the-aggregation-framework-in-mongodb>

## 简介

数据分析是许多应用的重要组成部分。MongoDB 提供了几个工具来使用聚合框架本地运行分析。

聚合框架基于管道的概念。其概念是从 MongoDB 集合中获取输入，该集合中的文档经过一系列的*阶段*。每个*级*对其输入执行一个操作。每个阶段的输入都是前一阶段的输出。所有输入和输出都是文档。

如果您熟悉 Javascript，那么当您链接方法时，概念是相同的。你可能遇到过这样的事情:

```
const myData = myArray.map( (item, i) => {
    //do something special
}).filter( (item, i) => {
    //We filter in this stage
}).reduce( (item, i) => {
    //I need to reduce in this stage
}) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们有三个阶段。一个*贴图()*，一个*滤镜()*和一个*还原()*。第一阶段(map)将 myArray 作为输入。这个方法的输出成为我的下一个方法(filter)的输入。如此等等，直到我最终不需要转换我的数据并拥有我需要的东西。

但是在 MongoDB 中，每个输入和输出都将是文档。最后，我们可以访问输出，就像查询之后一样。

因此，简而言之，聚合框架与管道一起工作。管道使用 MongoDB 集合。管道由阶段组成。每个阶段在其输入端执行一个数据处理任务，并生成文档作为其输出。该输出被传递到下一阶段。

那么，现在我们知道它是什么，我们如何使用它？

## 熟悉操作入门

我们将使用 MongoDB 提供的餐馆集合。你可以在这里下载[。将其保存到文件中。](%20https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json)

接下来，您需要将数据导入集合。我们将使用 *mongoimport* 来实现这一点。打开终端窗口，输入以下命令:

`mongoimport --db test --collection restaurants --drop --file PATH/TO/JSON/FILE`

确保您已经运行了一个 mongod 实例。这个命令将把我们的数据导入到*测试*数据库中的*餐馆*集合中。如果你愿意，可以随意重命名。

我们有现成的数据。我们将从一些基本操作开始，如匹配、投影、排序、跳过和限制阶段。

### 我们的文档是如何组织的

在我们的餐馆集合中，文档有以下字段:

*   _id 字段(在所有 MongoDB 文档中是必需的)
*   名称字段(字符串)
*   美食(串)
*   星星(整数)
*   地址(包含以下字段的嵌入式文档:
    *   街道(字符串)
    *   城市(字符串)
    *   状态(字符串)
    *   邮政编码(字符串) )

### $匹配

让我们从一个过滤器开始，寻找所有提供俄罗斯美食的餐馆:

```
>db.restaurants.aggregate([
    { $match: { cuisine: 'Russian' }}
])

{ "_id" : ObjectId("5977aaed3abbae8aef525fdc"), "name" : "Abe Fisher", "cuisine" : "Russian", "stars" : 1.2, "address" : { "street" : "828 Jaduv Boulevard", "city" : "Hiburu", "state" : "TX", "zipcode" : "00004" } }

{ "_id" : ObjectId("5977aaef3abbae8aef53e606"), "name" : "Abe Fisher", "cuisine" : "Russian", "stars" : 2.4, "address" : { "street" : "463 Zujew Street", "city" : "Peamazut", "state" : "NH", "zipcode" : "00304" } }

{ "_id" : ObjectId("5977aadc3abbae8aef4715f1"), "name" : "Abe Fisher", "cuisine" : "Russian", "stars" : 4.5, "address" : { "street" : "105 Buwza Heights", "city" : "Asovikufu", "state" : "MA", "zipcode" : "00488" } }

{ "_id" : ObjectId("5977aada3abbae8aef45bf79"), "name" : "Abe Fisher", "cuisine" : "Russian", "stars" : 3.5, "address" : { "street" : "1774 Emcek Key", "city" : "Mossofnat", "state" : "NY", "zipcode" : "02312" } }

{ "_id" : ObjectId("5977aadd3abbae8aef47bf64"), "name" : "Abe Fisher", "cuisine" : "Russian", "stars" : 0.4, "address" : { "street" : "1172 Kohis Road", "city" : "Jihnahac", "state" : "MS", "zipcode" : "02585" } }

{ "_id" : ObjectId("5977aae33abbae8aef4b8cbf"), "name" : "Abe Fisher", "cuisine" : "Russian", "stars" : 4.2, "address" : { "street" : "1582 Ogujab Pass", "city" : "Vihjuani", "state" : "MN", "zipcode" : "03000" } }

{ "_id" : ObjectId("5977aadc3abbae8aef46e0e3"), "name" : "Abe Fisher", "cuisine" : "Russian", "stars" : 3.6, "address" : { "street" : "1076 Cugvu Glen", "city" : "Ittupa", "state" : "NH", "zipcode" : "03726" } } 
```

Enter fullscreen mode Exit fullscreen mode

这相当于运行以下查询:

`db.restaurants.find({cuisine: 'Russian'})`

你可以注意到我们使用了**聚合**方法。这是用于运行聚合查询的方法。为了聚合，我们需要传递一个聚合管道。管道是一组文档。每个文档都指定了我们希望数据经过什么过程。

让我们在聚合查询中添加第二个阶段。

### $项目

*$project* 阶段允许我们控制输出。我们可以告诉 MongoDB 我们希望看到或不希望看到哪些字段。

```
db.restaurants.aggregate([
    { $match: { cuisine: 'Russian'}},
    { $project: {
        _id: 0,
        name: 1,
        stars: 1
    }
    }
])

{ "name" : "Abe Fisher", "stars" : 1.2 }
{ "name" : "Abe Fisher", "stars" : 2.4 }
{ "name" : "Abe Fisher", "stars" : 4.5 }
{ "name" : "Abe Fisher", "stars" : 3.5 }
{ "name" : "Abe Fisher", "stars" : 0.4 }
{ "name" : "Abe Fisher", "stars" : 4.2 }
{ "name" : "Abe Fisher", "stars" : 3.6 }

... 
```

Enter fullscreen mode Exit fullscreen mode

我的第二阶段告诉 MongoDB 只输出两个字段，name 和 stars。我也明确排除了 _id。默认情况下，如果未将 _id 字段设置为 0，则会显示该字段。

*$match* 阶段对集合进行过滤，并将结果文档一次一个地传递给 *$project* 阶段。 *$project* 通过相应地重塑字段来执行其操作，并将输出从管道中传递出来并返回给我。

让我们增加一个额外的阶段。

### $限额

*$limit* 阶段将限制管道返回的结果数量。像这样:

```
> db.restaurants.aggregate([
    { $match: {cuisine: 'Russian'}},
    { $limit: 3 },
    { $project: {_id: 0, name: 1, stars: 1}} 
    ])

{ "name" : "Abe Fisher", "stars" : 1.2 }
{ "name" : "Abe Fisher", "stars" : 2.4 }
{ "name" : "Abe Fisher", "stars" : 4.5 } 
```

Enter fullscreen mode Exit fullscreen mode

使用 *$limit* ，我只返回前三个文档。

*注:*我把*$限额*阶段**放在***$项目*阶段之前。如果我把 limit 阶段放在最后，这将意味着 project 阶段必须处理文档上的数据，而我无论如何都不会返回这些数据。通过将我的阶段按此顺序排列，项目阶段只处理三个文档。

### 再举一些例子

下面是一些你可以完成的任务的随机例子:

*   在本例中，我想将*星级*字段重命名为*评级*。我还想有大写的餐厅名称，我想通过评级排序的文件。

```
> db.restaurants.aggregate([ 
    {$project: {restaurant_name: {$toUpper: "$name"}, _id: 0, ratings: "$stars"}}, 
    { $sort: { ratings : -1}}, 
    { $limit: 5 } ])

{ "restaurant_name" : "MODERN LOVE", "ratings" : 5 }
{ "restaurant_name" : "LAUBERGE CHEZ FRANCOIS", "ratings" : 5 }
{ "restaurant_name" : "DAI DUE", "ratings" : 5 }
{ "restaurant_name" : "FRANKLINVILLE INN", "ratings" : 5 }
{ "restaurant_name" : "LAUREL", "ratings" : 5 } 
```

Enter fullscreen mode Exit fullscreen mode

返回两个字段:大写的 restaurant_name 和降序的 ratings。我使用了$toUpper 操作符来转换名称。 *$project* 阶段创建了一个名为 *restaurant_name* 的新字段，并赋予其大写值 *name* 。与现场*明星*的*评级*相同。注意，我们用一个 *$* 作为值的前缀，告诉 MongoDB 我们正在讨论另一个字段的值。

最后，让我们使用*$组*阶段。在本例中，我们将根据菜系类型对餐馆进行分组。每遇到某个菜系，我们就增加一个计数器 1。最后，我们将知道我们的集合中最常见的美食类型:

```
> db.restaurants.aggregate([ 
    { $project: { _id: 0, cuisine: 1}}, 
    { $group: { _id: "$cuisine", count: { $sum: 1}} }, 
    { $sort: { count: -1}}, 
    { $limit: 5} ])

{ "_id" : "American", "count" : 6183 }
{ "_id" : "Chinese", "count" : 2418 }
{ "_id" : "CafÃ©/Coffee/Tea", "count" : 1214 }
{ "_id" : "Pizza", "count" : 1163 }
{ "_id" : "Italian", "count" : 1069 } 
```

Enter fullscreen mode Exit fullscreen mode

在项目阶段，我们只保留我们下一阶段需要的东西，即烹饪领域。然后，我们在文档中对这些结果进行分组，其中 *_id* 字段将是特定的菜肴，count 字段将计算我们遇到该菜肴的次数。

接下来，我们对这些结果进行排序，首先得到最高的计数，我将结果限制为 5 个。在我们的集合中，我们主要有美式菜肴(不管是什么),有 6183 个文档，然后是中餐，有 2418 个文档...

这是一个关于聚合框架允许您做什么的快速介绍。当然还有很多可能性。如果你对此感到好奇，你可以在 [MongoDB 文档](https://docs.mongodb.com/manual/aggregation/#aggregation-pipeline)上阅读更多内容