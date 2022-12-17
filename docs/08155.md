# MongoDB 向导-你的魔杖得到升级！

> 原文：<https://dev.to/akazia_it/mongodb-wizards---your-wands-gets-an-upgrade>

MongoDB 3.4 已经发布，新版本提供了一些真正酷的特性。我想给你展示一些你可以用它们做的魔术。我还没有尝试过所有的方法，所以列表还不完整，但是我会在这里更新。不要害怕，你不需要学习任何复杂的魅力-一切都保持简单，因为它是，虽然功能越来越成熟..

聚合管道在 3.4 中得到了相当多的增强。像更多的操作符、表达式、数组元素使用的增强、字符串处理和分支支持。

对于新的数组操作符，我想强调我一直在等待的两个:`$in`和`$indexOfArray`。他们言出必行$in 返回一个布尔值，表明给定值是否在数组中，`$indexOfArray`返回给定值第一次出现的数组索引。

关于弦，那里有很多 UTF-8 处理增强加上，我的最爱之一。猜猜它在做什么…正确，它按照给定的分隔符分割字符串，并以数组的形式返回分割结果。

同样非常有趣的是一个控制表达式流的新选项。你可以使用`$switch`来评估一组 case 表达式。当它找到一个计算结果为真的表达式时,`$switch`将分支到它，执行相关的表达式并跳出。因此，对于开发人员来说，将它直接放在聚合管道中并不新鲜。这里有一个语法的快速浏览，你可以把它添加到你的魔咒书中:

```
 $switch: {
        branches: [
            { case: <expression>, then: <expression> },
            { case: <expression>, then: <expression> },
            …
        ],
    default: <expression>
    } 
```

Enter fullscreen mode Exit fullscreen mode

您可以点击[此链接](https://docs.mongodb.com/v3.4/release-notes/3.4/#aggregation)获取更多信息和聚合管道新特性的完整列表。

实际上也是聚合管道的一部分，但值得一提的是递归搜索的能力。神奇的是，在它的帮助下，当你需要回答一个最短路径的问题时，你再也不会头疼了。有一个有趣的[网上研讨会:在 MongoDB](https://www.mongodb.com/presentations/webinar-working-with-graph-data-in-mongodb?jmp=docs) 中使用图形数据，它详细解释了如何使用它，并提供了大量相关信息。对于速度快的蚱蜢，跳过前 20 分钟，直接跳到那里。但是要注意，仓促行事总是意味着失去一些东西——第一部分提供了很多信息。

在 MongoDB 3.4 中，与外界的联系得到了显著的改善。BI 连接器已经过重新设计，现在可以更容易地利用您的数据。新版本还包括对 [Apache Spark 连接器](https://www.mongodb.com/products/spark-connector?jmp=blog)的更新。如果你还没有检查，那么现在是时候了。您可以使用 Apache Spark 的强大功能，并添加 MongoDBs 聚合管道和快速内存预处理、分片的所有优势，以克服 Sparks 弹性分布式数据集(RDD)的一些缓慢问题。

回顾过去，你可能会有这样的印象:所有地方都在进步，而且，你是对的！我只提到了我开始使用的新特性，或者我可以直接用来改进的新特性。你可以从我在文中链接的文档中挑选其他的。我不会说 3.4 正在改变世界，但它改变了我的发展..为什么？看看左边和右边的小特征。例如，在 3.4 之前的`$split`中，您查询了集合，用 javascript 处理了结果，并可能开始进一步的查询。现在，您可以在一个平台上完成所有工作。那么变化是什么呢？这是更简单的代码，更少的行——只是可读性更好，所以更好维护，而且速度更快，因为你是在数据库上本地操作的。一个`$split`并不重要，但是如果你选择`$graphLookup`，你会发现一个很大的不同。

所以，MongoDB 巫师们，施展好魔法，发挥你们升级魔杖的全部力量吧！ *Lumos Maxima！*

`{ name : "Michael Höller",
eMail : "info@akazia.de"}`