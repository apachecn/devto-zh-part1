# 软件博客机器人的未知问题

> 原文：<https://dev.to/tra/untold-problems-of-a-software-blog---bots>

根据 Incapsula2015 年的一份报告，48.5%的网络流量来自机器人。如果你在运行一个*的小*网站(每天 10 到 1000 次访问)，你 85.4%的网络流量都是由机器人带来的。即使你碰巧运行了一个非常大的网站(每天 10 万到 100 万次访问)，你的网页流量中也有 39.7%是由僵尸程序产生的。

坦率地说，你的大多数观众都是由机器人组成的。(当我在维护一个德克萨斯宗教组织的网站时，从谷歌分析中发现 90%的访问该网站的人来自俄罗斯。)

少数机器人是“好机器人”——爬虫和网络扫描工具。他们的目标是访问网站并向另一个用户提供有价值的信息。搜索引擎根据关键词聚合结果，聊天机器人试图回答用户的自然语言查询，等等。Googlebot 是一个“好机器人”的典型例子(实际上在这篇博客文章的标题中有一幅手捧鲜花的图片)。

大多数机器人都是“坏机器人”——收集电子邮件并寻找内容进行窃取的抓取程序、DDoS 机器人、扫描网站安全漏洞的黑客工具、试图出售最新减肥药的垃圾邮件发送者、点击您广告的广告机器人等。(好吧，从有广告的博主的角度来看，广告机器人可能是“好”的，但对于为广告付费的公司来说，它们仍然相当糟糕。)

我在 2016 年 6 月发表过一次关于机器人的演讲- [这是它的幻灯片](https://docs.google.com/presentation/d/1gK6KmL_dtwk_8477Jc6GeLAipZMLoMVxFa9TIffggcQ/edit#slide=id.g142d681b2f_0_187)。但是我写这篇博客是为了回应软件博客给[带来的数不清的好处。那篇博文中的论点是正确的。但是任何关于博客的讨论都必须考虑到博客的受众。大多数网站的受众将是机器。以下是机器人可能导致的问题:](https://dev.to/liquidise/untold-benefits-of-a-software-blog)

**你的指标不可靠**——你可以雇佣一个僵尸网络来访问你的网站，上下滚动页面，执行 JavaScript 来触发你的分析软件，点击广告和其他链接，写关于你的博文的评论，喜欢你的博文，转发你的博文，等等。等。即使你不想伪造你的指标来提升你的虚荣心，机器人可能仍然会像正常用户一样避免被其他算法检测到。这可能意味着他们在前往最终目的地之前会访问多个“无辜”的网站(包括你的网站)。糟糕的分析使你的内容很难准确定位，否则你怎么知道你的内容是否真的对人类有用呢？

你在为机器写东西——互联网上的内容太多了，人类无法全部消化。所以他们依赖于聚合器和过滤器。但内容制作者随后意识到，他们不应该写高质量的内容。这是没有用的，因为没有聚合器和过滤器的帮助，没有人会发现高质量的内容。相反，内容制作者应该写出吸引聚合者和过滤者的内容。这是 SEO(搜索引擎优化)背后的最终前提，最终结论是人们用长尾关键词写文章来保证搜索引擎的访问。SEO 是[古德哈特定律](https://en.wikipedia.org/wiki/Goodhart's_law)(“当一个度量成为目标时，它就不再是一个好的度量”)的自然结果。搜索引擎依赖链接来决定网站是否受欢迎，这导致了[【链接农场】](https://en.wikipedia.org/wiki/Link_farm#History)的兴起。谷歌还负责[内容农场](https://en.wikipedia.org/wiki/Content_farm)的兴衰，如[eHow](https://en.wikipedia.org/wiki/EHow)——谷歌的算法曾经珍视这样的“新鲜内容”，只是后来随着[的“熊猫”更新](https://en.wikipedia.org/wiki/Google_Panda)改变了他们的想法。即使在今天，谷歌[仍然鼓励在内容生成中“数量重于质量”](http://relevance.com/how-google-ruined-content-marketing/):

> 虽然一篇高质量的文章可能会带动 1000 股，但 10 篇文章每篇能带动 120 股就更多了。用流量或者转化来代替份额。是同一个概念。通过这种方式，谷歌实际上是在鼓励我们将我们的内容商品化，而不是创造伟大的内容，不管这是不是有目的的。

这种反常的趋势最终是不可持续的，但它可能还会持续一段时间。营销人员已经在讨论[《华盛顿邮报》如何通过使用算法](http://buzzsumo.com/blog/future-lot-content/)快速生成更多内容，以及现有企业如何通过大规模生产低质量内容淹没来自新贵[的竞争。](https://www.businessesgrow.com/2016/09/19/crappy-content/)

**你的内容将与你脱离** -像 dev.to 这样的网站上的内容被 Black Hat SEO 专家编程的抓取器逐字逐句地转载到别处。这些专家希望用廉价的内容填充他们的网站...还有什么比复制粘贴更便宜的呢？如果你幸运的话，他们愿意给你一个反向链接，作为一封毫无意义的“感谢信”，感谢你提供他们迫切需要的内容。大多数时候，他们不会打扰。甚至像谷歌这样的“合法”参与者也通过使用[特色片段](https://support.google.com/webmasters/answer/6229325?hl=en)进入了获取和重用你的内容的行业。

然而，一种新的铲运机出现了——智能铲运机。他们可以在网站上搜索包含某些关键词的句子，然后使用“文章旋转”技术重写这些句子。尽管我觉得链接到黑色搜索引擎优化网站令人反感，但这里有一个关于 Kontent Machine 3 的 YouTube 演示，这是一个智能刮刀(所以你可以观察它是如何工作的)。这种输出足以取悦搜索引擎算法，但在人类也能享受这种输出之前，还需要做一些工作。(免责声明:我正在开发“内容聚合”软件，该软件可以从外部 CSV 文件中提取段落，然后将它们重新排列成独特的文章。我还在想一个不用帮助智能刮刀就能开源的好办法。)

我认为随着文本生成技术变得更好，人工智能变得更先进，人们可能会开始回收内容...淡化甚至消除图片中的内容创作者。机器策展人受到赞扬，而人类内容创作者被贬低或忽视。

我在文章[中对这种趋势做了更积极的描述，谁是电脑生成小说的观众？](https://dev.to/tra/who-are-the-audiences-of-computer-generated-novels)，在标题为“想要消费一个已有语料库的人”的部分。毕竟，外面有太多的数据，我们确实需要算法来理解它们，馆长在从混乱中恢复秩序方面的作用确实值得一些“赞扬”。这些算法令人兴奋。但是令人兴奋的算法也带来了负面的后果——我们必须意识到这些后果，并为此做好准备。

想象一个聊天机器人驻留在你的终端。你可以问这个聊天机器人一个编码问题，而不用去 StackOverflow 看一个人的名字或者头像

```
#> howdoi python for loop
#=>
#for idx, val in enumerate(ints):
#    print(idx, val) 
```

Enter fullscreen mode Exit fullscreen mode

那个世界已经存在。它叫做 [howdoi](https://github.com/gleitz/howdoi) ，一个开源的命令行工具。当然，它并不完美(StackOverflow 也不完美)，但 howdoi 最让我惊讶的是，它也可以做定性回答:

```
#>howdoi evolutionary algorithm
#>The pro: Evolutionary algorithms (EAs), as genetic algorithms (GAs), are general 
# purpose optimization algorithms that can be applied to any problem for which you
# can define a fitness function. They have been applied to nearly all conceivable
# optimization problems, see e.g. the conference series on „Parallel Problem
# Solving from Nature“. The con: EAs are generally much slower than any specific
# optimization algorithm adapted to the problem. I would apply them only if all
# standard optimization algorithms fail. 
```

Enter fullscreen mode Exit fullscreen mode

```
#>howdoi solve the halting problem
#>The normal approach is to constrain program behavior to an effectively calculable 
#> algorithm.  For example, the simply typed lambda calculus can be used to 
#> determine that an algorithm always halts.  This means that the simply typed 
#> lambda calculus is not Turing complete, but it is still powerful enough to 
#> represent many interesting algorithms. 
```

Enter fullscreen mode Exit fullscreen mode

人类写了这个内容。但是人类的努力没有得到认可，因为是算法(“howdoi”)在管理这些内容并将其展示给最终用户。请注意,“howdoi”的输出中也没有反向链接，这意味着“howdoi”在技术上是在抄袭。“howdoi”是未来，人类内容生成器的未来看起来并不好。

**结论**:我不是*必然*说“不要费心写博客，因为唯一会读它们的人是机器人”。写博客有很好的理由。要知道人类并不是这些帖子的主要受众。如果你将博客主要视为自我表达和与其他人交流的工具，那么你会走得很远。但是，如果你把博客当作获得名声的门票，你很可能会失败。

**校正**:

*   虽然 howdoi 目前确实在从事剽窃，但是[开发者已经意识到这一点](https://github.com/gleitz/howdoi/issues/152)并且[已经公开了一个 PR 来解决这个问题](https://github.com/gleitz/howdoi/pull/153)。