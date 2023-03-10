# 限制和好处一样重要

> 原文：<https://dev.to/pbeekums/limits-are-as-important-as-benefits>

每当我评估技术时，我通常看到的都是它的优点。不管是数据库、web 框架、编程语言、库等等。那些谈论技术的人往往要么是技术的粉丝，要么是技术的创造者。让人们使用它符合他们的最大利益。强调一项技术的积极方面有助于做到这一点。

这种态度不会让任何人成为坏人。这恰好是标准的营销实践。你和我可能会为我们已经建立的东西做同样的事情。如果没有，那么这就是我们在推销中得到的第一个反馈。[![](img/a17bc4db57d38d027683df5dd2ee4c67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5KlqPM5M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/7/achilles.png)

但是没有什么是完美的。

大多数东西在设计时都有一个或一组目的。尽管在某些情况下这些会失败。即使是旨在尽可能广泛使用的技术也会发现它并不出众的情况。

以 MySQL 为例。我爱 MySQL。已经用了好几年了。用它可以构建许多不同类型的应用程序。

然而，我不会使用 MySQL 进行分析或搜索。有些人尝试。它会工作一会儿。但是 MySQL 不是为大规模分析或搜索而设计的。当数据量达到一定程度时，速度会变得非常慢，而且这种减速不是线性的。它突然发生，几乎没有任何预兆。

另一方面，ElasticSearch 对于分析和搜索来说都很棒。它可以很好地处理这些情况。但是，尽管它的创造者正在努力使它更能抵御失败，它仍然不如 MySQL 可靠。MySQL 可以更容易地防止丢失用户的宝贵数据。

然而，发现弹性问题并不容易。开发人员试图确保他们的软件能够实际运行。好的软件很少会失败。然而，[还是会失败。](https://blog.professorbeekums.com/software-systems-will-fail/)一项技术如何处理这种故障至关重要，因为它决定了我们使用它的目的。

MySQL 很好地处理了故障，所以我将把它作为我的权威数据源。ElasticSearch 不太可靠，但它在大型数据集上执行分析和搜索查询的速度比 MySQL 快得多，所以我将使用它进行分析和搜索。

不了解这些限制可能会付出极高的代价。我见过许多公司用 MySQL 建立分析。刚开始还行。但是问题总是会突然出现。随着数据量的增长，解决这些问题变得异常困难。选择要么继续按现状前进，要么重建这个系统。

然而重建并不容易。将一个数据库换成另一个数据库是琐碎的反义词。我最喜欢的例子是 Yandex 花了 10 个人年和 3 个日历年从 Oracle 数据库转换到 Postgres 数据库。因此，选择是花大量的时间维持现状，还是花大量的时间重建。

有时候这甚至不是一个选择。有时候，确实没有办法让现有的技术在你需要的规模下工作。这使得重建变得更加可怕。在这种情况下，会有尽快找到替代者的压力。仓促的决定很少是最好的决定。仓促的决定是团队在这种情况下的首要原因。

能够通过为工作选择最佳技术来防止这些问题是最令人愉快的情况。然而，我们又回到了原点，因为一项技术的局限性很少被公开。ElasticSearch 恰好是少数几个尽最大努力记录其缺陷的公司之一，但这是开发者不常拥有的奢侈。

如上所述，构建快速原型很少会暴露大规模出现的问题。任何东西都可以作为原型。

你可以依靠对你正在评估的技术有经验的同事。但这种经历最有可能是通过艰难的方式获得的，而这对一家公司来说也是一种昂贵的方式。

最好的办法是，如果技术的推广者更清楚地知道他们实际上是为了什么而设计它，以及它的局限性是什么。从长远来看，这对他们实际上是有益的。越来越少的人会尝试用他们的技术去做一些不适合的事情。这意味着更少的人会对它有不好的印象。

人们在高压情况下会达到这些极限。这阻碍了他们冷静思考并说“好吧，下次我不会在这种类型的应用程序中使用这个东西。”他们会有压力。他们的反应更可能是“这东西****ing 烂透了！我再也不会用这个了。任何人都不应该使用这个！我告诉所有我认识的人不要用这个。”

防止这种反应将为营销技术创造奇迹。

不幸的是，标准营销实践之所以如此，是因为它行之有效。当技术 A 没有缺陷，而技术 B 有一长串缺陷时，我们自然会被技术 A 吸引，因为它看起来很完美。

作为该技术的消费者，我们需要做的是认识到这种反应。应该表扬技术 B 的制造者。技术 A 的制造者应该会遇到大量的质疑。让人们列出他们工作中的缺点的方法是开始给予那些有缺点的人更多的尊重。

*本帖最初发表于[blog.professorbeekums.com](https://blog.professorbeekums.com/limitations-tech/)T3】*