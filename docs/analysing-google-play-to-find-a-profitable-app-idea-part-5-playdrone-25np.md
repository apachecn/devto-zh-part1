# 分析 Google Play 以找到一个有利可图的应用创意——第五部分:PlayDrone

> 原文：<https://dev.to/alexhyettdev/analysing-google-play-to-find-a-profitable-app-idea-part-5-playdrone-25np>

到目前为止，我已经使用了 [PlayDrone](http://systems.cs.columbia.edu/projects/playdrone/) 数据来找出下载最多的[免费游戏](https://www.alexhyett.com/profitable-app-freemium-games/)、[付费游戏](https://www.alexhyett.com/profitable-app-paid-games/)和[付费应用](https://www.alexhyett.com/profitable-app-paid-apps/)是什么。有许多有用的查询可以运行，以尝试找到一个有利可图的应用程序的想法。例如，你可以查看下载量很大但用户评分很低的应用。然后你可以看看这个应用的评论，找出用户不满意的原因，做出更好的应用！如果你愿意的话，你甚至可以得到一份收入至少 1 万英镑的所有付费应用的列表。查看失败的应用程序也可能是有用的，通过运行查询来找到从未下载过的应用程序，然后找出原因，这样你的应用程序就不会遭受同样的命运。

你可以从[互联网档案馆](https://archive.org/details/android_apps&tab=about)下载一个 735MB 的 Json (JavaScript Object Notation)文件，其中包含截至 2014 年 10 月 31 日 Google Play 上 140 万个应用的详细信息。我写了一个控制台应用程序，将所有这些应用程序的详细信息插入到 SQL 中，以便可以对其进行查询，这很有趣。你可以在我的 GitHub 账户上找到源代码，这个账户有一个富有想象力的名字 [PlayDrone2SQL](https://github.com/alexhyett/PlayDrone2SQL) 。

## playdrone 2 SQL 的制作

下载了 735MB 的 Json 文件后，我开始将它转换成 SQL 模型。我用 Visual Studio 2015，SQL Server Express 和 Entity Framework 6 做了控制台 app。我是 Entity Framework 和 Linq 的忠实粉丝，在工作中广泛使用过它们。我从代码优先的方法开始，创建了表示存储在 Json 文件中的信息的类。

然后，我让 Entity Framework 创建数据库和表，并编写一些代码将文件读入内存并输出到 SQL。

我使用 [Newtonsoft 的 Json.NET](http://www.newtonsoft.com/json)将 Json 文件反序列化为我的应用程序对象列表。每个应用程序都有一个类别，我在一个单独的表中存储，只有 42 个。

这个程序的逻辑非常简单。对于每个应用程序，它首先检查该应用程序是否已经存在于数据库中。如果没有，它将检查数据库中是否存在该类别，如果没有，就添加它。然后，它将应用程序映射到实体框架模型，并将其保存到数据库。

然后，我在 Json 文件上运行它，并在命令窗口中观察它的进度。在我的慢速笔记本电脑上，将 Json 文件从磁盘加载到内存需要几分钟，一旦完成，它就会将文件反序列化到我的模型中。然后，我看着应用程序被添加到数据库中。到目前为止一切顺利。对于 140 万个应用程序，我预计这需要一点时间，所以我出去做了半个小时的其他事情。当我回来的时候，我希望它已经在列表中走了很长一段路，但它只完成了大约 25000 个。嗯，按照这个速度，大概需要 28 小时才能完成！

## 性能微调

所以我决定做一些性能调整。第一个调整是删除类别的查找。只有 42 个类别，在几百个应用程序被处理后，它们都已经被写入数据库。因此，在初始加载时，我将它们读入字典以用作 ID 查找，然后只在它们不存在时将它们添加到数据库中。

现在每分钟处理大约 1000 个应用程序，这将把时间减少到 23 小时。然而，该应用程序现在需要很长时间来检查它已经添加到数据库中的所有应用程序。

由于所有的应用程序都是按顺序添加的，所以我对数据库中的应用程序进行了计数，然后使用该计数从列表中选择下一个应用程序。我还在 StackOverflow 上做了一些搜索，发现了一些加速实体框架的调整。

### 臃肿的 dbContext

当实体框架处理大量记录时，dbContext 会变得臃肿，这会导致它变慢。解决这个问题的方法之一是重新创建 dbContext 并批量保存数据。所以每 100 个应用我会做一个数据库。保存 Changes()，然后重新创建 dbContext。

### 关闭轨迹变化

另一个常见的解决方法是关闭跟踪更改。每次实体框架将一个实体加载到内存中时，它都会创建一个更改树来跟踪内存中对它所做的任何更改。在不需要的时候关闭它可以显著提高应用程序的速度。

所以我最终用这样的方法来处置和创建我的 dbContext。

```
private static MarketDbContainer DisposeAndCreate(MarketDbContainer db)
{
    if (db != null)
    {
        db.Dispose();
    }

    db = new MarketDbContainer();
    db.Configuration.AutoDetectChangesEnabled = false;

    // The timeout was required as my laptop was being slow reading from disk
    db.Database.CommandTimeout = 300;

    return db;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能会认为经过所有这些变化后，处理速度会有很大的提高。不幸的是，它现在每分钟大约处理 1200 次，也就是大约 19 个小时来处理数据。一定有更快的方法。

## SqlBulkCopy 来救援

在更多的谷歌搜索之后，我发现了 SqlBulkCopy。我以前用过它，但是没有意识到它比实体框架快多少。我在 [developer fusion](http://www.developerfusion.com/article/122498/using-sqlbulkcopy-for-high-performance-inserts/) 上找到了一篇有用的文章，介绍了如何通过一个代码示例执行高性能插入，该代码示例允许我通过 SqlBulkCopy 使用现有对象。我选择以 100，000 个为一批来插入它们，以防错误导致我丢失它们(比如内存不足)。

在快速重写之后，我加载了我的控制台应用程序，并满怀期待地等待着。幸运的是，我不用等很久，大约 5 分钟就可以将 140 万条记录全部插入数据库。

所以简而言之，不要用实体框架进行大的插入。还建议关闭表中的任何索引。

因此，如果你想自己运行一些查询，你可以从我的 [GitHub 账户](https://github.com/alexhyett/PlayDrone2SQL)获取我的控制台应用程序。

当你下载 Json 文件时，你会发现有一个名为“metadata_url”的属性，它指向另一个 Json 文件，该文件包含关于该应用程序的更多细节，如价格和评级细分。我还没有做任何事情，但在未来，我计划扩展这个工具，以类似的方式处理这些，以及处理用户评论。