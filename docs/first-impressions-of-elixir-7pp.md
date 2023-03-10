# 对仙丹的第一印象

> 原文：<https://dev.to/jackmarchant/first-impressions-of-elixir-7pp>

Elixir 是一种基于 Erlang 的函数式编程语言。有人告诉我，它与 Ruby 非常相似，对开发人员体验和语言语法做了一些调整和改进。

[绕道——系好安全带]

我对 Elixir 感兴趣是因为我对函数式编程感兴趣，特别是 JavaScript。我从学习制作纯函数的技术开始，这样我可以更容易地测试我的代码。然后，我进一步学习了 JavaScript 中的组合、currying 和部分应用，特别是当使用 Redux 时知道这些很有用。

[喘口气，这是一个很大的嗡嗡声]

…

[足够的呼吸]

所以，后来(没有掌握以上任何一项)我决定学习函数式编程理论(基本上只是数学)碰碰运气。那也很有趣。

我翻来覆去，试图弄清楚 FP 是什么意思，老实说我很喜欢它——所以为什么不试着去学一门纯粹的语言(明白吗？)功能性。

[回到灵丹妙药…/绕道]

现在你知道了我为什么对学习诸如 Elixir 这样的函数式编程语言感兴趣，让我们一起踏上一段神话般的旅程，看看我学到了什么。

### 学习新事物很难

我注意到的关于 Elixir 的最好的事情之一——目前我已经使用了大约一周——是它非常注重开发者的体验。

我想 Elixir (José Valim)的创造者一定是看了 Erlang，觉得我们可以做得比这更好。最优秀的人会采纳好的想法，并让其他人更容易学习。

下面是 Elixir 作为开发人员做的或帮助你做的几件事(以列表形式，因为谁不喜欢好的列表呢？)

*   内置单元测试(运行*‘混合测试’*)
*   通过使文档成为模块分发的一部分来鼓励文档
*   interactive Elixir–iex >允许在终端中运行代码
*   比 Erlang 更容易理解的语法
*   模式匹配(那是什么？–继续阅读)

那是很多事情。你还想要什么？

所有这些都使得有意愿的开发人员更容易找到灵丹妙药并尝试一下。没有人愿意为了开始学习一门新的语言而在他们的开发环境中瞎折腾。

### FP 就是 FP

函数式编程真他妈厉害。不可小觑。我不想花超过 3 秒的时间遍历我的代码(并在那天解剖我的大脑)，因为我懒得创建一个具有清晰接口和签名的函数。我开始觉得一个超过 10 行的函数太长，做的事情太多。

说 Elixir 更好很容易，因为它对你应该写什么和如何写代码有严格的规定，但我认为用任何语言写垃圾代码都是可能的，只是用其他语言更容易。

假设 Elixir 是一种 FP 语言，那么所有的 Elixir 模块都遵循它的一般原则是有意义的。考虑到我早期使用 Elixir 的经历，我可以说我欣赏 JavaScript 语言的严格性，但是对于编写函数式 JavaScript 所具有的创造性和表现力，也有一些值得一提的地方——现在有很多人在谈论这个问题。

有趣的是，在 JavaScript 中发现函数式语言的强大功能后，我还没有看到更多的人使用函数式语言。也许他们只是没有写一篇很棒的文章？

### WTF？(什么特色？！)

为了与 Elixir 的功能联系保持一致，有一个叫做模式匹配的特性，我很想了解更多。我不认为这是长生不老药独有的特性，但这确实是我第一次遇到。

我的想法是，你可以将一个函数定义为另一个函数的副本，用值代替参数，当值等于传入的值时，它将运行那个函数，而不是下一个函数。

例如，我有一个接受列表的递归函数，但我只希望它在列表中有项目时运行(否则它会陷入递归循环)。

我的直觉是使用 if 语句检查是否有项目并提前返回——但是使用模式匹配，当第一个参数是空列表时，您可以说只需提前返回。您必须确保在您想要覆盖任何函数之前定义模式匹配函数。

这个概念将两种情况分成两个函数，而不是用一个函数来处理所有情况。作为初学者，这是一件很难实现的事情，但我很有兴趣看看它是否提高了代码的可读性。

### 我们去哪里？(我们现在去哪里)

函数式编程已经存在很久了，但是随着网络软件工程的成熟，开发人员开始质疑我们是如何做事情的，并寻找更好的东西。

长生不老药对我来说听起来像是一个新的挑战，而且有一些好的方面。现在似乎是我掌握它的最佳时机，所以我的目标将是变得更加适应它，能够从头开始一个项目，并在没有教程帮助的情况下自己构建一些东西。

**TLDR**

*   Elixir 看起来是一种学习更多函数式编程概念的有趣方式
*   Elixir 对文档、测试和可读代码的关注是我学习更多相关知识的动力——从其他阅读材料来看，它似乎具有很高的可扩展性。
*   开发人员的体验似乎是经过深思熟虑的——使其具有吸引力并且易于上手。
*   包管理系统，包括包分发似乎类似于 NPM。

### 至此

如果你已经读到这里，我祝贺你做得很好。休息一天。

以下是我迄今为止所做的一些灵药工作的链接:

*   [jackmarchant/misc](https://github.com/jackmarchant/misc)
*   [jackmarchant/todo-elixir](https://github.com/jackmarchant/todo-elixir)
*   [杂项](https://hex.pm/packages/misc)