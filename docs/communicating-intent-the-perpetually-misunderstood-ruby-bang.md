# 沟通意图:永远被误解的红宝石爆炸(！)

> 原文：<https://dev.to/sqlsuperhero/communicating-intent-the-perpetually-misunderstood-ruby-bang>

随着软件项目的增长，能够通过清晰合理的代码向其他开发人员展示您的意图是至关重要的。在本系列中，我将介绍一些小技巧来做到这一点。该系列的第一篇文章可以在我的博客上找到: [Ruby 的 Tap](http://tombroomfield.com/communicating-intent-1-tap/) 。

今天我们报道爆炸！)符号。在我看来，这是向其他开发人员传达意图时最常见、但却被误解的策略之一。虽然微妙，但如果坚持使用，它实际上非常有用。

首先要记住的是，ruby 中位于方法名末尾的`!`，比如`save!`本身并没有什么特别的作用。它构成了方法名的一部分，唯一的目的是传达关于该方法可能做什么的其他信息。

快速的谷歌搜索会导致很多不正确的信息。比如这里的，[这里](http://stackoverflow.com/a/18801459)，[这里](http://stackoverflow.com/a/31598104)，[这里](http://stackoverflow.com/a/16301829)，[这里](http://stackoverflow.com/a/16301928)，[这里](http://stackoverflow.com/a/6208446)，[这里](http://stackoverflow.com/a/6208856)，[这里](http://stackoverflow.com/a/6208417)，[这里](http://stackoverflow.com/a/6260608)，[这里](http://stackoverflow.com/a/33923572)，[这里](http://qr.ae/RO9KE9)和[这里](http://www.wellho.net/mouth/2893_Exclamation-marks-and-question-marks-on-ruby-method-names.html)。

这些都告诉你，爆炸符号只是在适当的位置修改对象。当然*在某些情况下*会这样做，但这绝对是*而不是*砰的意思。这方面的一个例子是`downcase`对`downcase!`。`downcase!`将修改现有的字符串，而`downcase`将返回一个新的字符串。

这是有用的，但是，逻辑很快就失效了。例如，`pop`法:

```
a = [1,2,3]
a.pop
a #=> [1,2]
```

`pop`修改了数组，但是没有`!`。

另一个例子是 Rails 中的`save/save!`方法。对于验证错误，`save`返回`nil`，而`save!`抛出一个错误。

在[工作流](https://github.com/geekq/workflow) gem 中，你使用`state!`移动到一个特定的状态。

这些方法都与修改对象无关，那么它们为什么使用 bang 呢？

幸运的是，Ruby 的创始人 Matz 对此有话要说:

> 霹雳(！)并不意味着“破坏性”，缺少它也不意味着非破坏性。爆炸标志的意思是“爆炸版本比非爆炸版本更危险；小心轻放”。由于 Ruby 有很多“破坏性”的方法，如果 bang 符号遵循你的意见，每个 Ruby 程序都会充满 bang，因此很难看。
> 
> ~松本幸弘
> 
> [来源](https://www.ruby-forum.com/topic/176830#773946)

所以我们开始吧。当你有可能更危险的方法时，使用 bang。我发现对任何爆炸方法发表评论都很好，解释了危险。

bang 的一些好的用例:

*   失败时抛出错误，而不是返回 nil。
*   强制提取绕过缓存并重新计算。
*   一种隐藏了大量复杂性或者具有性能含义的方法。
*   任何其他有副作用的方法都可能会产生意想不到的副作用。

Bang 是一个非常好的工具，可以让其他开发人员知道要小心行事，所以在有意义的时候使用它。在探索代码时，要特别注意寻找这些方法，并确保它们被正确使用。如果没有注意到危险，它们通常可以揭示错误或性能问题。