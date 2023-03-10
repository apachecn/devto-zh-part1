# 在计算机生成的作品中使用模板

> 原文：<https://dev.to/tra/using-templates-innbspcomputer-generated-works>

> "在你考虑替代方案之前，模板可能看起来是一种糟糕的造句方式."–[克里斯·普莱西](https://github.com/dariusk/NaNoGenMo-2015/issues/16)

你如何建立一个算法来构建‘人类可读’的小说？虽然有过几次尝试，但人类从未偶然发现这个特定问题的好“答案”……迄今为止。

不过，人们还是打算梳理一下这个“AI 难”的问题。NaNoGenMo(全国小说生成月)每年 11 月举行……要想参加，你要做的就是写一个可以生成 50000 字的算法，然后把你的源代码公开。我们之前讨论了为计算机生成的小说提供“结构”的技术，以使这些小说更易于人类阅读。

但是如果我们采用不同的方法来生成文本呢？如果我们不试图在计算机生成的作品中归纳结构，而是直接教计算机如何写小说，会怎么样？

写故事时，我们必须记住和使用一些规则。我们可以称这些模式为“语法”。我们必须教一个人如何写作。我们也可以“教”计算机如何作曲……通过硬编码文学的规则。我们可以把这个模型命名为模板。我们还可以编写“嵌套”模板，在规则中包含规则，尝试为生成的故事添加更多变化。

[花萼](https://github.com/maetl/calyx)是一种红宝石，可以用来快速定义模板。下面是如何定义这样一个模板的示例:

```
class HelloWorld < Calyx::Grammar
  start '{greeting} {world_phrase}.'
  greeting 'Hello', 'Hi', 'Hey', 'Yo'
  world_phrase '{happy_adj} world', '{sad_adj} world', 'world'
  happy_adj 'wonderful', 'amazing', 'bright', 'beautiful'
  sad_adj 'cruel', 'miserable'
end

HelloWorld.new.generate
#"Hello bright world."
```

Calyx 最初是为了生成 NaNoGenMo 小说[地牢比喻的游戏本](https://github.com/dariusk/NaNoGenMo-2015/issues/189)，这是一个由程序生成的地牢房间组成的自选冒险。我个人将它作为生成故事的一个很好的原型工具。[某地，某事](https://github.com/dariusk/NaNoGenMo-2014/issues/133)是另一部基于模板的计算机生成小说，尽管程序员用 Python 代替了 Ruby。事实上，找到模板以某种方式被使用的例子要比找到模板根本没有被*使用的例子容易得多。*

模板可能是制作计算机生成的文学作品的最简单的方法，并且已经被一些公司用于商业用途，例如[叙事科学](https://www.narrativescience.com)、[自动化洞察](https://automatedinsights.com)和 [Yseop](http://yseop.com/EN/home) 。这些公司制作基于现实生活数据的自动化报告和新闻故事，供人类消费。当我写了一个算法来生成小说[相信上帝的无神论者](https://github.com/dariusk/NaNoGenMo-2015/issues/45)时，我也试图产生一个类似的系统。

模板也很受“黑帽 SEO”专家的欢迎。这些专家对快速生成内容感兴趣，以满足搜索引擎的需求，不管这些内容是多么垃圾或重复。因此，这些专家求助于[文章旋转](https://en.wikipedia.org/wiki/Article_spinning):拿一篇预先写好的文章，然后用对等的单词替换大部分单词。因此，一篇文章可以作为生成数百个“独特”内容片段的基础。甚至有一种用于文章旋转的独特语法，称为 spin tax……并且有许多用语言编写的这种格式的解析器，如 [PHP](https://gist.github.com/irazasyed/11256369) 、 [JavaScript](https://github.com/johnhenry/spintax) 和 [Ruby](https://github.com/flintinatux/spintax_parser) 。Spintax 还被编写来生成[垃圾博客评论](https://gist.github.com/shanselman/5422230)，修改可能的回复，希望欺骗垃圾过滤器和人类将评论视为真实。

模板的主要问题是涉及到“手工劳动”。毕竟，你仍然需要一个人来制作计算机用来编写故事的模板。然而，这种“体力劳动”可以随着自动化的使用而减少。垃圾邮件制造者已经编写了算法，根据人类撰写的文章“自动”生成 spintax。虽然生成的模板的输出可能很难看，但它们可以在以后被人类清理掉。汤森路透在 2015 年也获得了一项专利，即[使用机器学习来基于预先存在的新闻材料语料库](http://www.google.com/patents/US20150261745)生成模板，然后由人类进行清理。

模板也可以被批评为避免实际上给机器增加‘创造力/智能’的问题。机器并没有真正受到启发，以与人类相同的方式书写…它真正做的只是遵循模板内编码的命令。但是其他文本生成方法，如[夏尔-RNN](https://github.com/karpathy/char-rnn) 和[马尔可夫链](https://en.wikipedia.org/wiki/Markov_chain)，在产生长篇故事方面失败了，尽管它们在技术上令人印象深刻，并且在短时间内读起来令人回味无穷。算法越聪明，创意输出越愚蠢……[至少目前是](http://qz.com/682814/i-want-to-talk-to-you-see-the-creepy-romantic-poetry-that-came-out-of-a-google-ai-system/)。

由于模板在生成人类可读的作品方面非常有效，因此在不久的将来，它们很可能会被用于各种不同的领域。很可能第一部人类可读的计算机生成小说将基于与其他算法技术一起使用的模板。

## 附录

甚至这篇博文也是用“spintax”生成的，而这个“spintax”是用谷歌上很容易找到的工具生成的。你可以在这里看到来源 Spintax [。产生的 spintax 非常可怕，需要大量的工作来清理……但人工智能一直在进步。谁知道接下来会发生什么？](https://gist.githubusercontent.com/tra38/5ca218e02b470a5b117468b21ab17215/raw/030827cb429ea69309d55e02a340c00b3a1f03b0/spintax.rb)

我只使用 spintax 作为模板概念的证明。我强烈不赞成文章旋转，谷歌也是。这就是为什么在熊猫更新其搜索引擎算法时，[试图惩罚这种做法。](http://www.seoblog.com/2014/08/panda-update-effectively-killed-autoblogs/)