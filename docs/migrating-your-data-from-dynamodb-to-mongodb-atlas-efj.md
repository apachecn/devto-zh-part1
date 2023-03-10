# 将数据从 DynamoDB 迁移到 MongoDB Atlas

> 原文：<https://dev.to/mongodb/migrating-your-data-from-dynamodb-to-mongodb-atlas-efj>

您希望从 DynamoDB 迁移到 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=dynamodb&jmp=dev-ref) 可能有很多原因。虽然 DynamoDB 可能是一组特定用例的好选择，但随着需求变得更加复杂，许多开发人员更喜欢减少客户端代码或额外技术需求的解决方案[。他们可能还想使用开源技术，或者需要某种程度的部署灵活性。在这篇文章中，我们将探讨为什么您可能会考虑使用 MongoDB Atlas 而不是 DynamoDB 的一些原因，然后看看您将如何着手迁移一个预先存在的工作负载。](https://www.mongodb.com/blog/post/q4-inc-relies-on-mongodb-atlas-to-boost-productivity-outpace-the-competition-and-lower-costs?utm_medium=dev-synd&utm_source=dev&utm_content=dynamodb&jmp=dev-ref)

## 得到建筑，更快...

[MongoDB Atlas](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=dynamodb&jmp=dev-ref) 是使用 MongoDB 并获得开发人员友好体验的最佳方式，在 AWS 上作为服务交付。MongoDB 的查询语言非常丰富，允许您在本地执行复杂的查询，同时避免在操作引擎和分析引擎之间移动数据的开销。使用 Atlas，您可以使用 MongoDB 的原生查询语言执行任何操作，从对单个键或范围的搜索、分面搜索、图形遍历和地理空间查询，到复杂的聚合、连接和子查询，而不需要使用额外的附加服务或集成。

例如，MongoDB 的聚合管道是一个强大的工具，用于实时执行分析和统计分析，并为仪表板生成预聚合报告。

[![](img/f523c2ead0b3a5bcdf141efc9a91da84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--au9AFqH5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image4-bi4cgu3tlk.png)

此外，MongoDB 将为您提供一些我认为非常酷的额外功能:

*   文档大小——MongoDB 本身可以处理最大 16MB 的文档。在 DynamoDB 中，每个项目被限制为 400KB，包括名称和任何本地二级索引。对于更大的东西，AWS 建议你在 DynamoDB 和 S3 之间分配存储空间。
*   部署灵活性——使用 MongoDB Atlas，您可以将完全托管的 MongoDB 部署到 AWS、Google 云平台或 Microsoft Azure。如果您决定不再在云中运行数据库，那么 MongoDB 可以在几乎任何硬件上的任何环境中运行，因此自我管理也是一种选择。
*   MongoDB 有一个惯用的驱动程序集，提供了几十种编程语言对数据库的本地语言访问。
*   MongoDB Atlas 提供了一种可查询的备份方法，用于在文档级别恢复数据，而不需要完全恢复数据库。
*   MongoDB Atlas 为您提供了 100 多个不同的实例指标，用于丰富的本机警报和监控。
*   多亏了[性能顾问](http://docs.atlas.mongodb.com/performance-advisor/)，Atlas 将帮助您找到正确的索引。Performance Advisor 实用程序一直处于打开状态，帮助您确保查询高效而快速。

## 入门

在本教程中，我们将从现有的 DynamoDB 表中取出一个基本的数据集[并将其迁移到 MongoDB Atlas 中。我们将使用一个免费的 M0 集群，因此在您评估 MongoDB Atlas 的优势时，您也可以免费这样做。](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.Java.02.html)

这篇博文做了几个假设:

您已经[在您将要导入数据的计算机上安装了 MongoDB](https://docs.mongodb.com/manual/installation/) (我们需要 MongoDB 附带的`mongoimport`工具)您已经注册了一个 [MongoDB Atlas 帐户](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=dynamodb&jmp=dev-ref)(M0 实例对于本演示是免费的)

首先，我们将回顾一下 AWS 中的表格:

[![](img/7bf4d0212dca0a9898b9505d8c9e41bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xhtB0-AH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image6-yj19ur1tyi.png)

此表包含有关电影的数据，包括电影的发行年份、片名，以及包含在子文档中的有关电影的其他信息。[![](img/bb3380de7cae53a6ff8d07175b9c75f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lePowxtK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image3-3i0dc3i2mj.png)

我们希望将这个基本数据集引入 MongoDB Atlas，以获得更好的查询、索引和长期管理数据的方法。

首先，如果您在生产环境中，请确保您的应用程序已经停止写入，以防止新条目进入您的数据库。您可能希望创建一个临时登录页面，并禁用与 DynamoDB 的新连接。完成后，在 AWS 面板中导航到您的表。

选择牌桌后，单击顶部的“操作”。找到“出口到。csv”选项并单击它。

[![](img/2b0101e5d1295b6fe7e6ce3521719f07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--37RjS4pm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image5-6vozp33uqs.png)

现在您将从 DynamoDB 获得一个 CSV 格式的数据导出，让我们快速看一下:

```
$ more ~/Downloads/Movies.csv
"year (N)","title (S)","info (M)"
"1944","Arsenic and Old Lace","{ ""actors"" : { ""L"" : [{ ""S"" : ""Cary Grant"" }, { ""S"" : ""Priscilla Lane"" }, { ""S"" : ""Raymond Massey"" }] }, ""directors"" : { ""L"" : [{ ""S"" : ""Frank Capra"" }] }, ""genres"" : { ""L"" : [{ ""S"" : ""Comedy"" }, { ""S"" : ""Crime"" }, { ""S"" : ""Romance"" }, { ""S"" : ""Thriller"" }] }, ""image_url"" : { ""S"" : ""http://ia.media-imdb.cimg/M/MV5BMTI3NTYyMDA0NV5BMl5BanBnXkFtZTcwMjEwMTMzMQ@@._V1_SX400_.jpg"" }, ""plot"" : { ""S"" : ""A drama critic learns on his wedding day that his beloved maiden aunts are homicidal maniacs, and that insanity runs in his family."" }, ""rank"" : { ""N"" : ""4025"" }, ""rating"" : { ""N"" : ""8"" }, ""release_date"" : { ""S"" : ""1944-09-01T00:00:00Z"" }, ""running_time_secs"" : { ""N"" : ""7080"" } }" 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错，让我们开始使用 MongoDB 的开源工具将它导入我们的 Atlas 集群。

导入您的数据让我们开始将数据迁移到 MongoDB Atlas 中。首先，[发布一个新的免费 M0 集群](https://www.linkedin.com/pulse/introducing-m0-free-tier-mongodb-atlas-jay-gordon/)(m0 非常适合用于演示，但是如果您要投入生产，您将需要选择一个不同的层)。一旦你有了一个新的 M0 集群，你就可以[将你的本地 IP 地址](https://www.youtube.com/watch?v=leNNivaQbDY)加入白名单，这样你就可以访问你的 Atlas 集群了。

接下来，您需要使用`mongoimport`实用程序将 Movies.csv 的内容上传到 Atlas。我将提供我的连接字符串，我可以直接从我的 Atlas 控制面板中获取，这样`mongoimport`就可以开始导入我们的数据:

[![](img/c85e26cedaf66da615f0545af2172f70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LyhnaCEP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image7-9dn45d4wfr.gif)

现在我可以在我的`mongoimport`命令中输入这个以及其他一些重要的选项:

```
mongoimport --uri "mongodb://admin:PASSWORD@demoabc-shard-00-00-a7nzr.mongodb.net:27017,demoabc-shard-00-01-a7nzr.mongodb.net:27017,demoabc-shard-00-02-a7nzr.mongodb.net:27017/test?ssl=true&replicaSet=demoabc-shard-0&authSource=admin" --collection movies --file ~/Downloads/Movies.csv --type csv --headerline
2017-11-20T14:02:45.612-0500 imported 100 documents 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的文档已经上传，我们可以使用 MongoDB Compass 登录到 [Atlas 并查看我们的数据:](https://www.mongodb.com/blog/post/your-mongodb-atlas-toolkit-logging-into-mongodb-atlas-with-compass?utm_medium=dev-synd&utm_source=dev&utm_content=dynamodb&jmp=dev-ref)

[![](img/b4c2c6d644f86c601e75c4e8279c5c3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tsi4siH9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://webassets.mongodb.com/_com_assets/cms/image2-g9s1y87xqk.gif)

<center>![](img/c5f2a0992d5bea3e0cf283890d277ea6.png)</center>

这很酷，但我想做一些更高级的事情。幸运的是，MongoDB 的聚合管道将赋予我们这样做的能力。

我们需要在这里连接到我们的 shell 我可以在我的 Atlas 集群的总览面板中点击“连接”按钮，找到外壳的连接说明。

一旦我登录，我可以开始玩一些不同的聚合；这里有一个基本的，告诉我们我们收藏的 1944 年上映的电影总数:

```
db.movies.aggregate(

    // Pipeline
    [
        // Stage 1
        {
            $match: { year : 1944 }
        },

        // Stage 2
        {
            $group: { _id: null, count: { $sum: 1 } }
        },
    ]
); 
```

Enter fullscreen mode Exit fullscreen mode

使用 DynamoDB，我们必须将数据库集群连接到 Amazon EMR，这增加了成本、复杂性和延迟。

您可以从 MongoDB Atlas UI 配置备份、确保网络安全以及为您的数据配置额外的用户角色。

[立即注册 MongoDB Atlas，开始更快地构建更好的应用](https://www.mongodb.com/cloud/atlas?utm_medium=dev-synd&utm_source=dev&utm_content=dynamodb&jmp=dev-ref)。