# 不要让程序员孤单

> 原文：<https://dev.to/donut87/dont-let-programmers-alone-bhh>

最近，我发现了一小段类似这样的代码:

```
function isElementSpecial(htmlElementToBeChecked) {
  var result
  if(document.querySelector(htmlElementToBeChecked).classList.contains('special')) {
    result = true
  } else {
    result = false
  }
  return result
} 
```

我畏缩了。最初的震惊过后，我的第一个想法是:“这个开发者在写这个的时候到底在想什么？”我不得不看了几次，以确保上面的代码确实只做了一件事。检查 HTML 元素是否有类`special`。
由于我没有时间研究这段代码是如何以及为什么会出现的，我做了每个程序员都会做的事情，迅速将代码浓缩到其核心。

```
function isElementSpecial(htmlElementToBeChecked) {
  return document.querySelector(htmlElementToBeChecked).classList.contains('special')
} 
```

因为我知道我的同事中谁写了这段代码，所以我很想直接问他为什么要以如此复杂的方式写一张简单的支票。幸运的是他已经回家了。所以我有几分钟时间去思考哪里出了问题。一些事实

1.  代码来自我们的前端测试系统
2.  负责的同事是在该领域有一些经验的资深程序员
3.  他自己在 CasperJS 的基础上构建了我们的测试框架
4.  他正忙于保持我们前端测试的稳定

看第三点。这是他自己建造的！
没有人审查、质疑或批评他的代码。
在我看来，如果有人能定期、短时间地回顾他的变化，这本来是可以避免的。
作为程序员，我们构建复杂的(通常是大型的)系统。对所有代码行有完整深入的了解是不可能的，即使是一个人写了所有代码。因此，忘记想要重构或重写的那一行或方法是很自然的。此外，附加到您的代码上的风险更高。为什么这是一个坏主意在这里[有很好的解释](https://dev.to/lpasqualis/12-reasons-to-avoid-individual-code-ownership)。据我所知，只有两种方法可以减少这一切。第一个是结对编程，第二个是代码审查。如果由于某种原因结对编程不可行(例如，分布式团队)，那就只剩下代码审查了。对于好的代码评审，有许多好的“手册”来指导该做什么和不该做什么(例如 [This](https://dev.to/samjarman/giving-and-receiving-great-code-reviews) )。
在我的工作场所，我们进行团队代码评审，尽管我们的大部分代码是在配对时完成的。每天早上我们问自己‘我们有什么东西要展示给团队吗’，然后打开投影仪，我们有整整半个小时的时间来查看代码差异并提出问题。这经常会暴露出无用的注释、不明确的变量/方法名以及其他代码样式问题。我们也对我们团队产生的代码有了一个大致的概念，我们的总线因子也大大增加了。
简而言之:做代码评审，经常做。很有可能让你的代码变得可以理解，增加总线因子，减少对代码的附加。