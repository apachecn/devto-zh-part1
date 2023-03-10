# 我们离开了 Clojure。以下是我会怀念的 5 件事。

> 原文：<https://dev.to/appcanary/we-left-clojure-heres-5-things-ill-miss>

10 月 11 日，Appcanary 依靠大约 8500 行 clojure 代码。12 日，我们降到了零。我们在代码库中添加了另外 5700 行 Ruby 代码来代替它。菲尔将会讨论我们离开的原因，以及在今年的 RubyConf 上我们在这里和[学到了什么。现在，我想谈谈我会想念什么。](http://rubyconf.org/program#prop_19)

### 1)口齿不清的喜悦

[![XKCD #297](img/0f9466469ec11cdbe3136d8fed98ce01.png)T2】](https://xkcd.com/297/)

写 lisp 有一种魔力。艾伦·凯称之为有史以来最伟大的单一编程语言。保罗·格拉厄姆称之为秘密武器。你可以在 lisp <sup id="fnref1">[1](#fn1)</sup> 优雅、思维开阔的力量上找到成千上万的单词。我不认为我的 Lisp wizardry 博客帖子的版本会特别原创或独特，所以如果你想知道更多关于使用括号的痛苦和狂喜，请阅读[保罗·格拉厄姆](http://paulgraham.com/avg.html)。

Clojure 的伟大之处在于，虽然 Ruby [可能是一个可接受的 lisp](http://www.randomhacks.net/2005/12/03/why-ruby-is-an-acceptable-lisp/) 、
和 lisp[可能不是一个可接受的 lisp](http://steve-yegge.blogspot.ca/2006/04/lisp-is-not-acceptable-lisp.html) ，但 Clojure 不仅仅是一个可接受的 lisp。如果我们避开类型系统的雷区，Clojure 解决了 Steve Yegge 在前面的链接 <sup id="fnref2">[2](#fn2)</sup> 中讨论的其他 4 个问题。

### 2)不变性

clojure 中的核心数据结构是不可变的。如果我将`car`定义为`"a dirty van"`，没有什么可以改变这一点。我可以稍后命名其他的东西`car`，但是任何引用第一个`car`的东西都将永远引用`"a dirty van"`。

这很棒，原因有很多。首先，您可以免费获得并行化——因为没有什么会改变您的集合，映射或减少其上的某些功能可以 hadooped 到您想要的任意多个云，而无需更改您的算法。

对你的代码进行推理也容易得多。拉里·沃尔有一句名言:

> [Perl]更希望你远离它的客厅，因为你没有被邀请，而不是因为它有一把猎枪。

他说的是私有方法，但大多数语言中的可变性也是如此。你调用某个方法，谁知道它是否改变了你使用的值？你不希望这样，但是你没有猎枪，坦白地说，你很容易在不知不觉中改变状态。考虑 Python:

```
str1 = "My name "
str2 = str1
str1 += "is Max"
print str1
# "My name is Max" print str2
# "My name" 
list1 = [1, 2, 3]
list2 = list1
list1 += [4, 5]
print list1
# [1, 2, 3, 4, 5] print list2
# [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

在一个字符串上调用`+=`会返回一个新的，而在一个列表上调用`+=`会使它发生变异！我必须记住哪些类型是可变的，以及`+=`是给我一个新的对象还是根据它的类型改变现有的对象。谁知道当你开始通过引用将变量传递到其他地方时会发生什么呢？

没有改变国家的选择就像摆脱你的脸书账户一样自由。

### 3)数据优先编程

远离面向对象语言是非常自由的。

我想为扑克游戏设计一个模型。我先列出名词 <sup id="fnref3">[3](#fn3)</sup> :“牌”、“副牌”、“手”、“玩家”、“庄家”等。然后我想到了动词，“发牌”，“下注”，“弃牌”等等。

现在怎么办？下面是一个典型的 StackOverflow 问题，展示了这样的设计所带来的困惑。庄家是玩家的一种还是单独的一类？如果玩家手中有牌，那么这副牌是如何记录剩下的牌的？

在一天结束时，编写扑克游戏程序的工作就是编纂游戏的所有实际规则，而这些规则最终将由一个`Game` singleton 来完成大部分工作。

如果您从考虑数据和对数据进行操作的函数开始，有一种自顶向下解决困难问题的自然方法，这可以让您快速迭代您的设计(见下文)。你有一些代表游戏状态的数据结构，一个代表玩家可能采取的行动的结构，以及一个将游戏状态和行动转换到下一个游戏状态的函数。该函数编码了扑克的实际规则(在许多其他更小的函数中定义)。

我发现这种编程风格非常自然，令人满意。当然，你可以在任何语言中这样做；但是我发现 Clojure 将我引向它，而 OO 语言将我推离它。

### 4)单元测试

你的大部分代码都是由纯函数组成的。一个纯函数是指对于给定的输入总是给出相同的输出——这听起来是不是很容易测试？您只需为您的功能编写测试，而不是建立测试工具数据库和模拟。

当然，测试与外界交流的代码的边缘需要模仿，而集成测试从来都不是小事。但是，您首先要测试的是代码库中超级复杂的业务逻辑。您的业务所依赖的业务逻辑，例如计算您的 OpenSSL 版本是否容易受到 HeartBleed 的攻击。

Clojure 让您将这段代码变成一个无需设置复杂状态就可以测试的纯函数。

### 5)重构

下面是一个典型的 clojure 函数

```
(defn  foo  [a  b]  ;; some code here  (let  [c  (some-function  a  b)]  ;; a ton of  ;; complicated code here  ))) 
```

Enter fullscreen mode Exit fullscreen mode

在 lisp 语言中，带括号的块被称为“形式”。`foo`表单是外部表单，它包含了`let`表单，后者表面上包含了其他做复杂事情的表单。

我知道`let`表单中所有复杂的代码不会改变任何状态，它只依赖于`a`和`b`变量。这意味着将这段代码重构到它自己的函数中就像选择两个匹配的括号之间的所有内容并剪切粘贴出来一样简单。如果您有一个支持 paredit 风格的 lisp 表单导航的编辑器，您可以以闪电般的速度重新排列代码。

* * *

1.  这类文章中我最喜欢的是马克·塔弗的忧郁
    [躁郁症患者 Lisp 程序员](http://www.shenlanguage.org/lambdassociates/htdocs/blog/bipolar.htm)。他将 lisp 描述为一种由杰出的失败者设计的语言。在大学的时候，我把这些都吃光了。我的成绩是成为 lisp 程序员一半要求的明显证据。 [↩](#fnref1)

2.  我知道 clojure 的`gensym`不是一个健康的宏系统。但是，如果您对健康宏有强烈的看法，因为它们与可接受的 lisps 有关，那么这篇文章可能不适合您。 [↩](#fnref2)

3.  郑重声明，我知道这不是设计面向对象程序的“正确”方法，但是我不得不承认这一点的事实证明了我的观点。 [↩](#fnref3)