# ORM 的微妙问题，以及如何避免它们

> 原文：<https://dev.to/joncalhoun/subtle-issues-with-orms-and-how-to-avoid-them>

> *本帖原帖发布于[calhoun . io](https://www.calhoun.io/subtle-issues-with-orms-and-how-to-avoid-them/)T3】*

在我的 Web 开发课程中，我收到的最常见的变更请求之一是停止使用 GORM，转而使用 Go 标准库中的`database/sql`包。

当我收到这样的反馈时，我经常会问:“为什么？”

我这么问并不是因为刻薄，而是因为我真的想知道人们不喜欢 GORM 的什么。他们是否有 ORM、第三方库或其他方面的问题？是什么让他们相信`database/sql`方案是一个更好的选择？

令我震惊的是，绝大多数要求这种改变的人实际上并没有一个好的理由，或者提供的理由实际上并不适用于他们。

事实是，我真的不能责怪他们。只需要花一两个星期的时间逛逛 subreddit，你就会很快开始相信 ORM 显然是撒旦的后代，不应该被使用。

在这篇文章中，我希望能弥补这一点。

我不指望说服每个讨厌 ORM 的人改变主意，也不指望每个人都开始使用 ORM。相反，我希望教育每个人，让他们能够自己做出明智的决定，对于那些使用 ORM 的人，我希望教育他们一些应该努力避免的问题。

## ORMs 可以隐藏复杂性

ORMs 可以让编写隐藏大量复杂性的代码变得非常容易。通常这是一件好事——我们不希望一直自己编写复杂的查询——但是当这些东西连接在一起产生意想不到的查询时，就很难设计出可高效伸缩的数据库。

Ruby on Rails 的活动记录可能是这里最大的罪魁祸首，但实际上这可能发生在任何 ORM 上。事实上，即使没有 ORM 也可能发生，但 ORM 往往会掩盖正在发生的事情，足以让坏事从缝隙中溜走。

首先，我们来讨论一下我的意思。假设你正在编写一个应用程序，在你的代码中的某个地方，你写了这样的代码:

```
# I'm using rails but this applies to Go as well
user.orders.each do |order|
  # ... use the order
end 
```

Enter fullscreen mode Exit fullscreen mode

当这段代码运行时，它可能会在后台执行一些 SQL。虽然一开始这可能没有问题，但随着时间的推移，这可能会成为一个问题。例如，如果你开始把不同的子句连接在一起，你可能会得到这样的结果:

```
user.orders.not_shipped.high_value.with_issue.each do |order|
  # ... use the order
end 
```

Enter fullscreen mode Exit fullscreen mode

随着查询变得越来越复杂，它们的性能可能会下降。当我们开始处理更复杂的查询时，这一点变得尤其明显，比如带有条件子句的多个连接。

当这种情况发生时，设计数据库的工程师将很难做出决策，因为他们对正在发生的事情没有清晰的了解。他们不知道哪些列需要索引，或者哪些连接需要优化，除非他们扫描整个应用程序，挖掘任何生成 SQL 查询的代码。

当然，我们可以添加一些分析，并开始测量哪些查询花费的时间最多，但这总是被动的。我们不能积极主动，回避潜在的重大问题。我们不能规定哪些查询应该运行，哪些不应该运行，因为它们会让我们的数据库慢下来。

正如我之前所说的，这不仅限于 ORM，而是 ORM 的一个更大的问题，因为开发人员可以编写复杂的查询，而实际上并没有看到正在生成的 SQL，因此不知道他们正在损害应用程序的性能。

另一方面，编写 SQL 并没有掩盖这一点，所以当你编写一个复杂的查询时，很难说“哦，我不知道！”

这让我们想到了 ORM 的另一个主要问题。

## ORM 让开发者保持无知

ORM 允许开发人员对他们正在使用的技术以及他们的决策将如何影响应用程序的整体性能一无所知。

在小型应用程序中，这通常无关紧要——一个 SQL 数据库可以相对容易地处理数千条记录。但是随着应用程序的扩展，这些问题变得更加突出。迭代数千条记录可能没问题，但迭代数百万条记录将开始产生负面影响。

编写纯 SQL 的主要动机之一是，开发人员必须学习足够的 SQL 来理解他们编写的复杂查询，因此他们应该现实地了解它们将如何影响应用程序的性能。如果他们正在编写通过特定属性进行连接或过滤的查询，他们可能也知道足够多的信息来确定需要创建哪些索引来保持性能。

## 回避这些问题

虽然 ORM 可能会出现这两个问题，但事实是它们都不是由 ORM 直接引起的。相反，这两者都是由糟糕的代码设计和缺乏教育造成的。

减轻这些问题的最好方法不是停止使用 ORM，而是学习更好的设计模式。教育您的团队成员和您自己，并纳入可靠的代码审查，以便如果不熟悉 SQL 的开发人员需要更新您的代码，熟悉他们的更改的后果的开发人员可以审查任何特定于数据库的更改。

一种方法是在代码中创建一个数据库层，并将所有数据库交互隔离到该代码中。这可能是一个包，也可能分成几个包。这里重要的是，最终创建 SQL 语句的唯一代码包含在应用程序的这一层中。

```
package database

type DB struct {
  // ...
}

func (db *DB) UserOrders(user *User) ([]Order, error) {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

通过以这种方式编写代码，您可以清楚地将数据库交互与代码的其余部分分开。当这里的代码发生更改时，您还可以轻松地合并更好的代码审查，允许熟悉 SQL 和您的数据库设计的多个开发人员在这些更改交付生产之前审查它们。这也为可能不太熟悉 SQL 开发人员提供了一个培训机会。

虽然这种方法更加严格，因为您需要为您希望应用程序的其余部分能够访问的每个查询公开函数，但随着时间的推移，测试、维护和保持效率要容易得多。

这种方法最酷的一点是，使用 ORM 与否并不重要。在上面的例子中，你知道我们在做什么，但是我不需要用`database/sql`或`gorm`包写任何代码，因为这根本不重要。ORM 可能被用来帮助构建 SQL 查询，但它不是必须的。我们甚至可以从使用 ORM 转移到使用纯 SQL，而不改变应用程序中的任何其他代码。

## 但是 ORMs 拖慢了你的 app！

人们很难谈论 ORM 而不跳出来说，“但是 ORM 会减慢你的应用程序！请使用另一个库来提高性能。

正如马克·贝茨所说的那样:“(T2)围棋界喜欢标杆。它被它们迷住了。

正如 Mark 在他的帖子中继续解释的那样，这种对速度和基准的痴迷带来的问题是，很少有应用程序实际上需要尽可能快。相反，他们只需要“足够快”。

最终用户不会注意到 31 毫秒和 30 毫秒响应时间之间的差异。现在，如果 ORM 导致了 100 毫秒的延迟，那么当然，你可以提出这个论点，但是在实际应用中，使用 ORM 的实际速度成本可以忽略不计。它不到应用程序总延迟的 1%。

与其在这里花太多时间，我建议你看看马克的帖子:

*   [http://www . metabates . com/2017/03/03/you-benchmarking-the-wrong-thing/](http://www.metabates.com/2017/03/03/youre-benchmarking-the-wrong-thing/)

它不是专门针对 ORM 或 web 应用程序的，但这一点仍然成立。对于大多数应用程序来说，除了使用 ORM 导致的很小的速度下降之外，还有其他更重要的因素需要考虑。

## 我们已经知道 SQL 了就不应该再去学新的东西！

这大概是我听到的最常见的避免 ORM 的理由，我也认同。如果你或你的团队已经知道 SQL 并且喜欢它，那么使用 ORM 是一个坏主意。

这种思维方式的问题是它只适用于一组开发人员——那些已经非常了解 SQL 并且更喜欢使用它的人。

另一方面，有一大群开发人员要么 **(a)** 不太了解 SQL，要么 **(b)** 更喜欢使用 ORM 或其他 SQL 构建库。

我教的大多数人都属于第一类——他们不太了解 SQL。事实上，他们中的许多人都是第一次学习 web 开发，所以将 SQL 添加到已经庞大的学习内容列表中不太可能有好结果。

相反，我发现工作方式类似于原始 SQL 的 ORM(或 SQL builder)是更好的选择。这不仅有助于初学者更快地入门和运行，而且有助于他们学习 SQL。例如，您可以在 GORM 中启用日志记录，以查看您编写的每段代码最终执行的 SQL 查询，代码看起来与 SQL 非常相似。

```
db.Where("email = ?", "jon@calhoun.io").First(&user) 
```

Enter fullscreen mode Exit fullscreen mode

想知道(大概)这样生成的 SQL 是什么？

```
SELECT * FROM users WHERE email='jon@calhoun.io' LIMIT 1 
```

Enter fullscreen mode Exit fullscreen mode

正如我所说的，它们看起来没什么不同，可以成为学习的好工具。

所以是的，如果你已经知道 SQL 并且喜欢它，你就不应该使用 ORM。但这并不能证明 ORM 是个坏主意。它只是表明，如果你知道并喜欢 SQL，那么你显然不应该使用 ORM。

## 总结...

如果你是 SQL 新手，或者你只是想使用 ORM，那就去用吧。他们没有任何“坏”或“邪恶”的东西。当别人告诉你他们不好的时候，他们真正表达的是一种看法。这种偏好是由他们非常不同的教育背景或团队经历造成的，他们可能有效地使用过 ORM，也可能没有。

如果你不喜欢 ORM，那很好。给你更多的力量。但是请不要仅仅因为你不喜欢 ORM，就告诉所有人 ORM 是糟糕的工具。通过让初学者相信他们需要在开始之前学习你所知道的一切，你正在积极地让他们更难进入开发，而这根本是不可能的。

## 你喜欢这篇文章吗？加入我的邮件列表！

如果你喜欢这篇文章，请考虑将[加入我的邮件列表](https://www.calhoun.io/subtle-issues-with-orms-and-how-to-avoid-them/#subscribe)。

我大概每周会给你发一封邮件，让你知道我最近正在写的或者已经发表的新文章或者视频(比如这篇)。没有垃圾邮件。不要出售你的电子邮件。我会像对待自己的收件箱一样对待你的收件箱。

作为对您加入的特别感谢，我还将向您发送我的课程 [Web Development with Go](https://www.usegolang.com) 的截屏和电子书样本。