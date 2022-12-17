# 静态类型的不可违背的承诺

> 原文：<https://dev.to/aussieguy/the-non-broken-promise-of-static-typing>

*最初发布于[我的博客](http://anthonybruno.com.au/2017/09/19/The-non-broken-promise-of-static-typing.html)T3】*

不久前，我读了一篇关于静态类型不能防止软件中出现 bug 的文章。文章恰如其名:[静态打字的失信](https://dev.to/danlebrero/the-broken-promise-of-static-typing)。作者通过生成和比较 GitHub 库的“bug 密度”分数进行了研究。bug 密度分数是通过获取每个存储库标记为“bug”的问题的平均数量来确定的。

结果表明，静态类型的语言比动态类型的语言有更多的错误。作者根据结果得出结论:

> “图表中缺乏证据表明更高级的类型语言将使我们免于编写错误，这非常令人不安。”

虽然这篇文章提出了很好的观点，并在原创研究方面做出了努力，但我一直认为这些观点是错误的。我坚信当使用静态类型语言时，会出现更少的错误。然而，我从来没有任何适当的证据来支持我的说法...**直到现在！**

进入:[早报](https://blog.acolyer.org)，一个总结科技白皮书的博客。它最近发布了一篇关于同一主题的文章，名为:[键入还是不键入:量化 JavaScript 中可检测的错误](https://blog.acolyer.org/2017/09/19/to-type-or-not-to-type-quantifying-detectable-bugs-in-javascript/)。

这篇文章涵盖了一项同名的研究。其中，研究人员查看了 GitHub 托管的 JavaScript 项目中的 400 个已修复的错误。对于每一个 bug，研究人员试图看看添加类型注释(使用 TypeScript 和 Flow)是否会检测到该 bug。结果呢？使用类型注释可以检测出大量的 **15%** 的 bug。随着错误的减少，很难否认静态类型的价值。

虽然这些结果显示了使用静态类型的好处，但是人们仍然会更喜欢特定的类型系统。那么，让我们听听你的意见吧！你喜欢什么类型的系统，为什么？