# 构建一个由 Amazon Lex、Lambda 和 MongoDB Atlas 支持的声控电影搜索应用程序——第 1 部分

> 原文：<https://dev.to/mongodb/building-a-voice-activated-movie-search-app-powered-by-amazon-lex-lambda-and-mongodb-atlas---part-1-192d>

### 简介

本教程分为 3 个部分:

第 1 部分:Lex 概述、演示场景和数据层设置

[第 2 部分:设置和测试 Amazon Lex bot](https://www.mongodb.com/blog/post/aws-lex-lambda-mongodb-atlas-movie-search-app-part-2?utm_medium=dev-synd&utm_source=dev&utm_content=lex1&jmp=dev-ref)

[第 3 部分:部署一个 Lambda 函数作为我们的 bot 实现逻辑](https://www.mongodb.com/blog/post/aws-lex-lambda-mongodb-atlas-movie-search-app-part-3?utm_medium=dev-synd&utm_source=dev&utm_content=lex1&jmp=dev-ref)

因为这是我们博客系列的第一部分，让我们现在就开始深入研究吧。

### 亚马逊 Lex 是什么？

亚马逊 Lex 是由 AWS 提供的深度学习服务，用于支持对话机器人(更常见的是“聊天机器人”)，这些机器人可以是文本激活的，也可以是语音激活的。值得一提的是，亚马逊 Lex 是支持 Alexa 的技术，Alexa 是亚马逊 Echo 产品和移动应用程序中可用的流行语音服务(因此得名 Lex)。亚马逊 Lex 机器人被构建来执行动作(如订购披萨)，在亚马逊行话中被称为 ***意图*** 。

请注意，每个机器人可能会执行多个意图(如“预订航班”和“预订酒店”)，每个意图都可以由不同的短语启动(称为 ***话语*** )。这就是 Lex bots 的自然语言理解(NLU)能力大放异彩的地方——你定义几个样本话语，让 Lex AI 引擎推断这些话语的所有可能变化(Lex' AI 引擎的另一个有趣方面是它的自动语音识别技术，它允许)。

让我们用一个虚构的电影搜索场景来说明这个概念。如果您创建了一个 *SearchMovies* 意图，您可能希望将一个示例话语定义为“*我想搜索一部电影*”，因为您希望它是用户表达其电影搜索意图时会说的话。但是你可能很清楚，人类倾向于用许多不同的方式表达相同的意图，这取决于他们的心情、文化背景、语言能力等...因此，如果用户键入(或说)“我想找一部电影”或“我想看一部电影”,会发生什么呢？好吧，你会发现 Lex 足够聪明，能够理解这些短语与“*我想搜索一部电影*”的意思相同，从而触发“搜索电影”的意图。

然而，正如我们的祖先罗马人会说的那样，如果用户的话语偏离你定义的样本话语太远，lex 将停止检测匹配。例如，虽然“*我想搜索一部电影*”和“*我想看一部电影*”被检测为我们的样本话语的匹配项(*我想搜索一部电影*)，但“*我想看一部电影*”不是(至少在我执行的测试中是这样)。

我从那个小实验中得出的临时结论是，Lex' AI 引擎尚未准备好为《银翼杀手》的复制体或《西部世界》的主机提供动力，但它肯定可以在各种情况下有用(我相信 AWS 的研究人员正在努力完善它)。

为了实现这个意图(比如提供用户正在寻找的电影名称)，Amazon Lex 通常需要一些附加信息，比如演员姓名、电影类型和电影上映年份。这些额外的参数在 Lex 术语中被称为 ***插槽*** ，它们是在特定的 Lex 提示符后一次收集一个。

例如，在检测到启动 *SearchMovies* 意图的话语之后，Lex 可以询问以下问题来填充所有需要的槽:

电影类型是什么？(填充*流派*槽)

你知道在那部电影中扮演角色的男演员或女演员的名字吗？(填充*铸件*槽)

这部电影是什么时候上映的？(填补*年*槽)

一旦所有需要的槽都被填满，Lex 就试图通过将所有的槽值传递给一些执行必要操作的业务逻辑代码来实现这个目的，例如，在电影数据库中搜索匹配的电影或预订航班。正如预期的那样，AWS 推广了自己的技术，因此 Lex 内置了对 Lambda 函数的支持，但您也可以“向客户端返回参数”，如果您想在应用程序代码中处理实现，您将需要使用这种方法(与 [Amazon Lex 运行时服务 API](http://docs.aws.amazon.com/lex/latest/dg/API_Operations_Amazon_Lex_Runtime_Service.html) 一起使用)。

### 演示机器人场景

你猜怎么着？这将是一个很短的部分，因为我们将在这个博客系列中实现的场景正是我上面描述的“虚构的例子”(多么巧合！).

事实上，我们将构建一个机器人，允许我们在电影数据库中搜索电影。我们将使用的数据存储是一个在 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=lex1&jmp=dev-ref) 中运行的 MongoDB 数据库，它非常适合不想设置和管理基础设施的开发人员和开发人员。

说到数据库，在我们开始构建我们的 Lex bot 之前，是时候将我们的电影数据库部署到 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=lex1&jmp=dev-ref) 了。

### 数据设置和探索

要设置电影数据库，请遵循这个 [GitHub 资源库](https://github.com/rlondner/mongodb-awslex-searchmovies#mongodb-atlas-setup)中的说明。

请注意，为了将数据库转储文件保持在 GitHub 的每个文件 100MB 的限制之下，我包含的数据库并不完整(例如，它不包含 1950 年之前发行的电影——向查理·卓别林的粉丝致以诚挚的歉意)。

现在，让我们来看看这个数据库中的一个典型文档(史密斯夫妇于 2005 年发布):

```
{
    "_id" : ObjectId("573a13acf29313caabd287dd"),
    "ID" : 356910,
    "imdbID" : "tt0356910",
    "Title" : "Mr. & Mrs. Smith",
    "Year" : 2005,
    "Rating" : "PG-13",
    "Runtime" : "120 min",
    "Genre" : "Action, Comedy, Crime",
    "Released" : "2005-06-10",
    "Director" : "Doug Liman",
    "Writer" : "Simon Kinberg",
    "Cast" : [
        "Brad Pitt",
        "Angelina Jolie",
        "Vince Vaughn",
        "Adam Brody"
    ],
    "Metacritic" : 55,
    "imdbRating" : 6.5,
    "imdbVotes" : 311244,
    "Poster" : "http://ia.media-imdb.cimg/M/MV5BMTUxMzcxNzQzOF5BMl5BanBnXkFtZTcwMzQxNjUyMw@@._V1_SX300.jpg",
    "Plot" : "A bored married couple is surprised to learn that they are both assassins hired by competing agencies to kill each other.",
    "FullPlot" : "John and Jane Smith are a normal married couple, living a normal life in a normal suburb, working normal jobs...well, if you can call secretly being assassins \"normal\". But neither Jane nor John knows about their spouse's secret, until they are surprised to find each other as targets! But on their quest to kill each other, they learn a lot more about each other than they ever did in five (or six) years of marriage.",
    "Language" : "English, Spanish",
    "Country" : "USA",
    "Awards" : "9 wins & 17 nominations.",
    "lastUpdated" : "2015-09-04 00:02:26.443000000",
    "Type" : "movie",
    "Genres" : [
        "Action",
        "Comedy",
        "Crime"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经强调了我们用例感兴趣的属性。每个电影记录通常包括主要演员(存储在字符串数组中)、电影可以分类的流派列表(存储在字符串数组中)和发行年份(存储为 4 位整数)。

这是我们将在 Lex bot(我们将在第 2 部分创建)中利用的 3 个属性，也是我们负责查询电影数据库的 Lambda 函数(我们将在第 3 部分构建)中利用的 3 个属性。

将这些属性存储为字符串数组是确保我们的 bot 具有响应性的关键:它们允许我们构建小的多键索引，这将使我们的查询比完全集合扫描(regex 查询将触发这种扫描)快得多。

### 总结

在这篇博文中，我们介绍了 Amazon Lex 的核心概念，并描述了我们将在第 2 部分中创建的 Lex bot 的场景。然后，我们将一个示例电影数据库部署到 MongoDB Atlas，探索了一个典型电影文档的结构，并确定了我们将在第 3 部分构建的 Lambda 函数中使用的字段。然后，我们回顾了在这些字段上使用辅助索引来加快查询速度的好处。

我只是触及了所有这些主题的表面，所以这里有一些额外的内容提供给那些努力学习更多内容的人:

亚马逊 Lex 如何工作

关于[索引](https://docs.mongodb.com/manual/indexes?jmp=adref)和[多键索引](https://docs.mongodb.com/manual/core/index-multikey?jmp=adref)的 MongoDB 文档

[使用 MongoDB 进行索引调优和评估](https://www.mongodb.com/presentations/webinar-index-tuning-and-evaluation-using-mongodb?jmp=adref)Daniel Farrell 的网络研讨会

我希望对 Lex 的介绍能引起你足够的兴趣，让你继续我们的第二部分之旅！

*关于作者——拉斐尔·伦德纳*

Raphael Londner 是 MongoDB 的首席开发人员，专注于云技术，如亚马逊网络服务、微软 Azure 和谷歌云引擎。此前，他是 Okta 的开发者拥护者，也是身份管理领域的创业企业家。你可以在 Twitter 上关注他，时间是 [@rlondner](https://www.twitter.com/rlondner)