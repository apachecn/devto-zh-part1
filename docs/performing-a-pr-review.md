# 执行公关审查

> 原文：<https://dev.to/dijitalmunky/performing-a-pr-review>

所以你在用 git？太棒了。但是接下来就是你的第一个拉取请求(PR)。你应该期待什么？或者也许是时候对别人的代码进行第一次公关审查了。这也令人望而生畏。没有人喜欢被批评，但是当代码有问题时，它需要被提出来。

在我近 20 年的编程生涯中，我一直在与这种情况作斗争，最初是以代码审查的形式，现在是以拉请求的形式。我做过[谷歌搜索](https://www.google.ca/search?q=how+should+you+perform+a+pull+request+review)，读过关于它的领导文章，最重要的是，我自己也在努力克服它。令人惊讶的是，这似乎是一套在大专计算机科学类型的程序中普遍没有得到很好发展的技能(至少在我的经验中)。不管怎样，也许通过我的痛苦和经历，你会发现一些有用的东西。

# 原则

我喜欢带着一些原则进行代码评审。我发现，无论你是评审者还是被评审者，这些原则都有助于整个过程更加顺利。

## 原则 1:要灵活

以开放的心态进行公关评估。编程既是科学，也是艺术。多年来我学到的一件事是，技术正确性和迂腐不一定等同于好代码。例如，如果代码太慢，让用户感到沮丧，那么变量名都遵循一种模式真的有关系吗？此外，我记得有一段时间，一个开发人员如此专注于用 ORM 编写好的代码，以至于他们认为这太棒了。问题是，这是一个批处理过程，必须将千兆字节的数据从一个数据库复制到另一个数据库。使用事务性 ORM 基本上是一种充满问题的方法，因为它需要将每个记录加载到一个对象中进行传输。另一种方法是使用批量传输工具在数据库之间传输数据，并最小化内存开销。然而，这位开发人员如此确信，因为他优雅的代码是完美的，所以当问题提出来时，他无法超越他的代码对于真正问题的技术正确性，所以它进入了生产。很快，随着数据集的增长，我们开始看到内存问题，这个过程开始失败...

关于这一点，也要记住我们都有不同的处理问题的方式和不同的编码风格。最后，如果代码库中有多种风格，维护人员是否能理解代码真的很重要吗？例如，如果名称的其余部分对团队来说是直观的，那么您在实例变量上遗漏了前导下划线真的有关系吗？或者，如果你的开始鸡嘴唇(又名花括号或{)与你的方法声明在同一行或在它的下一行真的有关系吗？不管怎样，代码仍然编译和执行相同的代码...

## 原则二:保持好奇

当进行公关评估时，把它当作一个学习的机会。公关双方都有机会学习。不要吹毛求疵，说“这是错误的，因为 X”之类的话，而是问一个问题，“这是一个有趣的方法，是什么让你走上这条路的？”如果你真的认为有什么东西坏了，你还可以更进一步，添加一个建议。例如，“你处理这个集合的逻辑很好，简单而可靠。但是，我想知道，我们是否可以通过使用新的`.ForEach()`收集方法来改进它，以便运行时引擎可以并行或多线程地优化和处理收集？”以这种方式做事会让人相信这个人所做的事情，但会问一些开放式的问题来开始关于如何改进的对话。

## 原则 3:这不是针对个人的

在团队中工作时，每个人都要意识到代码只是代码，这一点很重要。尽量不要把它当成作者(自己或者别人)的延伸。虽然有这种感觉是合乎逻辑的(毕竟有人用自己的大脑和想法写的，所以其中有很多自我的反映)。然而，这些感觉确实需要排在团队目标和代码目的之后。例如，我可能会为自己代码中的一个聪明的技巧而自豪，或者为我认为是一种特别优雅的编写方式而自豪。然而，如果代码对于其他人来说很难维护，或者对于最终用户来说非常慢，那么它可能需要改变。我经常遇到这样的开发人员，他们通过别人对他们代码的看法来衡量自我价值(我公开承认，在我的职业生涯中期，我也曾是这样的人，但我认为现在我已经过了那个阶段)。这种想法对所有参与者来说都是毁灭性的。帮你自己一个忙，想想如何摆脱“属于你的”代码。自从我明白了这一点，我的工作生活变得快乐多了。此外，我发现人们现在更容易和自由地接近我。

## 原则 4:不要忘记人的因素

我发现在当今时代，我们经常把技术放在我们和他人之间。发短信而不是面对面聊天或打电话就是一个例子。不要误解我的意思，像 Slack 这样的短信和聊天系统很神奇，带来了巨大的好处，但我们经常会忘记它们的局限性和缺点。人是天生的社会性动物，在近距离面对面的情况下会有不同的表现。

另一个很好的例子是，在 PRs 和代码审查的环境中，许多 git 系统(Github、BitBucket、Gitlab)允许在 PRs 上和整个 PRs 中放置注释。然而，这可能会导致问题，因为这些系统消除了面对面的交流，而公关审查本质上是“检查某人的工作”。当你只使用这些系统时，那么多的非语言交流和其他社交暗示都消失了。不要误解我，像这样的事情是有用的，有它们的目的，我们不能忘记我们是社会人。我建议了解你的受众并进行调整(见原则 1:要灵活)。我曾经和那些只使用这种交流系统的人一起工作过，因为他们不会因为事情不尽如人意而生气。然而，在同一个团队中，我曾与那些这种方式不太管用的人共事过(他们似乎总是感觉受到了攻击)。我发现重要的是知道什么对不同的队友有效，并进行调整。当我不确定团队成员在我审阅 PR 时可能会有什么反应时，我通常会采取以下措施:

1.  我使用评论系统作为标记问题的一种方式，包括对问题可能是什么的简要描述(在写这些描述时，我大量应用原则 2)。这只是为了不忘记任何东西，这样我们都可以快速找到受影响的代码。仅此而已。

2.  当我完成评估后，我会亲自或通过电话与该人坐下来，逐步说明我在公关中的关注点(我也很小心，不只是向该人宣读评论...有人在那里听我说话，自动纠正我写的话中任何潜在的冒犯)。再次，原则 2 的大量应用在这里被证明是有用的。

# 总结

所以你走吧...我作为被评审者和评审者的公关评审方法。基本上，归结起来就是谦虚，把它当成一个学习的机会。

用威尔·惠顿的不朽名言来说，“不要做个混蛋。”