# 从我的第一个开源贡献中得到的教训

> 原文：<https://dev.to/andy/lessons-from-my-first-open-source-contribution>

当我从我的编码训练营毕业时，我有很多关于如何找到工作并成为“理想候选人”的想法。你可能已经听过了:

*   “为开源项目做贡献！”
*   “写技术博客！”
*   “研究算法！”
*   “不要研究算法，你在浪费时间！”
*   “学习[ *插入流行框架*；大家都在用！”
*   "与开发者/招聘者/公司建立关系网！"
*   "做你热爱的兼职项目！"

这些都是很好的建议，但我最终只是学习和独处，因为我觉得我没有任何东西可以贡献给别人。“谁会看我写的东西？”和*“我不能给 Rails 或 Angular 增加任何价值，”*是一些让我怀疑自己的想法。

最重要的是，我成为了我的编码训练营的助教，给了我一个自满和少做事的借口。

<figure>

[![Patrick from Spongebob crossing off "Nothing" on a checklist](img/28b1d6020b0b449bc0cf6f6e8d159809.png)T2】](https://i.giphy.com/media/26ufnwz3wDUli7GU0/giphy.gif)

<figcaption>Clearly easier to do nothing.</figcaption>

</figure>

值得庆幸的是，作为一名助教，我接触到了总是有问题要问的初学者，其中一个问题让我第一次对 T2 的 Ruby gem Faker 作出开源贡献，这是一个生成假数据的库。不，我不只是想添加更多的星球大战的引用；有一个不同的问题。

### 问题，设想解决办法

我的学生试图使用 gem 中的一个类来生成一个到比利·穆雷图片的链接。然而，当使用默认参数运行该方法时，我得到了一个参数错误:

```
NoMethodError: undefined method `match' for 200:Fixnum 
```

我查看了 Faker 的代码库，发现了这个方法:

```
 def image(grayscale = false, width = 200, height = 200)
        raise ArgumentError, "Width should be a number" unless width.to_s.match(/^\d+$/)
        raise ArgumentError, "Height should be a number" unless height.to_s.match(/^\d+$/)
        raise ArgumentError, "Grayscale should be a boolean" unless [true, false].include?(grayscale)

        grayscale == true ? "https://fillmurray.com/g/#{width}/#{height}" : "https://fillmurray.com/#{width}/#{height}"
    end 
```

在我看来，这个方法没有任何问题，但是我和我学生的机器都没有成功运行代码。我检查了我们的版本是否与最新版本匹配，结果是匹配的。也许默认参数没有被转换成带有`to_s`的字符串，因此`match`方法被作为 Fixnum 在`200`上调用。

所以，我最终把它分支下来，做了一些看似修复它的编辑，在我的控制台上运行代码，它工作了！我遵循了投稿指南，并把[作为我的第一个拉请求。](https://github.com/stympy/faker/pull/908)

### 实解

<figure>

[![Disney Alice in Wonderland falling down the rabbit hole](img/045284010ec677d8c9981d79b83723fe.png)T2】](https://i.giphy.com/media/swtiK9jRfE0zS/giphy.gif)

<figcaption>Into the rabbit hole we went...</figcaption>

</figure>

几天后，我得到了回应。有人有同样的问题，想知道为什么代码库中的代码不起作用。他们问我运行的是什么版本的 Ruby，还问我是否能解释这个 bug。我明白他为什么问我的 Ruby 版本，但我也不明白为什么原始代码不工作。我检查并更新了我的 Ruby 版本，然后运行代码。得到同样的错误。嗯（表示踌躇等）...

然后我突然想到:也许我本地的 Faker 文件不正确。我花了几分钟思考如何找到它们，然后我打开了文件。你瞧，我的本地文件的方法没有将任何参数转换成字符串！

但是我有最新的版本。为什么我的代码与 GitHub 上的代码不匹配？因此，我搜索了这些问题，并注意到合并的拉请求的日期在最新版本之后。我关闭了我的拉请求，完成了我的第一个开源贡献！

### 现在...我学到了什么？

虽然这真的没什么贡献，但我很高兴我掉进了错误的兔子洞。比起仅仅注意到我的本地文件不是最新的，我学到了更多关于为开源做贡献的东西。

还有很多技术细节，但对我来说真正重要的是我又开始工作了。当我什么都不做的时候，动力对我不利:什么都不做比做什么更容易。几个月前我觉得太害羞了，现在我又开始写技术类的帖子了。我工作得越多，就越有信心做得更多。

综上所述，我希望你喜欢阅读我的帖子！我肯定会再次写作。在那之前，各位开发者。