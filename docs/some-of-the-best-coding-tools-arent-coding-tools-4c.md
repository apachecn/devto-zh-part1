# 一些最好的编码工具并不是编码工具

> 原文：<https://dev.to/craser/some-of-the-best-coding-tools-arent-coding-tools-4c>

```
"That which hinders your task... is your task." -Sanford Meisner 
```

Enter fullscreen mode Exit fullscreen mode

好了，你已经有了卓越的烹饪，IntelliJ 是你意志的无缝延伸，而 [Charles](https://www.charlesproxy.com/) 正在监视你的网络流量。QUnit，jUnit & Jenkins 已经联手，像 Tron 的小浮动“位”一样支持你。

好的开始。

但是一些最好的编码工具并不是专门的编码工具。我们来谈谈*那些*。

密码管理器:测试登录，地址&信用卡。

你已经在使用一个密码管理器来保存你的 dev box、test server、Jenkins、吉拉、Splunk、GitHub、StackOverflow 以及你工作所需的十几个其他登录信息，对吗？很好。

它可以做得更多。

当我需要测试某一类型的帐户时，我已经准备好了一个帐户来输入[1 密码](https://1password.com/)。伪造的万事达卡、维萨卡、美国运通和贝宝账户也是如此。如果我需要测试国际运输表单，我有安特卫普、伦敦、北京、迪拜、利马、首尔、哈拉雷和温哥华的地址。再也没有复制&粘贴，再也没有错别字。

不，这是对实际自动化测试的替代。但是当你收到客户服务的通知说“直接发货人不能使用国际运输表格”时，这*真的*有帮助。救命啊！”

代码片段工具:代码样本、分支名称、票据链接等。

TextExpander 、 [Breevy](http://www.16software.com/breevy/) 和 [Alfred](https://www.alfredapp.com/) 是这个地区的常年最爱。 [Dash](https://kapeli.com/dash) 也很受欢迎， [IntelliJ](https://www.jetbrains.com/help/pycharm/creating-and-applying-live-templates-code-snippets.html) 等 ide 对片段有很好的内置支持。

它们都很好，但我喜欢 TextExpander，主要是因为它内置了许多很棒的功能，让我可以在我机器上的任何应用程序中使用我的片段。

TextExpander 在后台运行，监听你的键盘输入。当您键入一个特定的字符串时，它会立即用一个存储的值替换它。例如:

| 我打字... | 我明白了... |
| --- | --- |
| tstampx | 2017.11.28-12.21.48 |
| 今天 | 11 月 28 日星期二 |
| 昨天 | 11 月 27 日星期一 |

我也用它来纠正错别字:

| 我打字... | 我明白了... |
| --- | --- |
| 返回到 | 返回 |
| Chirs | 克莉丝 |

你明白了。

[java.text.DecimalFormat](https://docs.oracle.com/javase/8/docs/api/java/text/DecimalFormat.html) 使用符号来表示特定于地区的货币符号的位置。你*知道怎么打 a 吗？我也没有。所以我把那个字符绑定为一个片段，现在当我键入 **currx** 时，得到的是。*

| 我打字... | 我明白了... |
| --- | --- |
| 货币 | ¤ |

我多久用一次？很少。但是我再也不用去寻找那个符号了。

当我在网页中需要占位符文本时，我只需键入 **loremx** ，就会得到 200 个单词的[伪拉丁语废话](http://www.lipsum.com/)。简单，但非常有用。

当您使用 TextExpander 在后台运行脚本并发出输出时，真正的魔法就开始了。(它将直接运行 shell 脚本& JavaScript。)我输入 **jtitlex** ，TextExpander 运行一个脚本，从吉拉的 REST API 中获取我正在处理的票据的标题。如果我输入 **tlx** ，我会得到那张票的 URL。tkx 给我它的吉拉 ID。

我为什么要做这种事？回想一下你收到的每一封询问“frob 问题进展如何？”当我得到这些信息时，第一行总是明确地说明*正在讨论的是什么问题，以及票号、标题和票系统中的票的链接。这是使用几个 TextExpander 代码片段完成的，所以只需要三秒钟，而且我从来不需要切换窗口。*

良好沟通的一部分是迅速消除困惑。snippet 工具是一个强大的工具，可以让这变得更容易。

我有另一个名为 [vm-boilerplate](https://github.com/craser/practical-dev-tools/blob/master/vm-boilerplate) 的脚本，它读取一个 [Velocity](http://velocity.apache.org) 模板文件，并生成一些 Java 样板文件来绑定必要的输入值。所以我只需键入 *vmtx* ，将文件名传递给 TextExpander，然后 TextExpander 运行我的 vm 样板脚本，并将输出放到我正在使用的任何编辑器中。处处管用，打字少，没有错别字，没有参数缺失。(因为经过几十年的激烈冲突，我们仍然没有解决`fileName`对`filename`的争论。)

我还有一个名为 [tebind](https://github.com/craser/practical-dev-tools/blob/master/tebind) 的小脚本，用于从命令行或其他脚本绑定 TextExpander 片段。所以片段不一定是硬编码的值；我可以基于其他流程动态地重新绑定它们。

TextExpander 不仅仅是一个文本替换工具。它的脚本功能允许您自动执行任何快速的信息查找，或者触发自动生成文本的脚本。

待办事项经理:保持你的行动一致。

选择使用什么待办事项列表管理器，如何使用，甚至是*是否*使用一个都是非常个人化的。如果你的系统有效，我绝不会冒昧地告诉你如何改进它。

我使用 [OmniFocus](https://www.omnigroup.com/omnifocus) ，但是还有*许多*好的选项。多年来，我一直使用 [Moleskine 记事本](https://us.moleskine.com/12-month-daily-planner-black/p0269?variantName=z1DaaFormat&variantValue=Pocket%20(cm%209x14))，效果非常好。也用过一段时间[的东西](https://culturedcode.com/things/)，喜欢。在 dev.to 上有一个关于在 Gmail 中构建一个伟大的系统的重要提示。

对我来说，能够点击`Alt-space`，输入待办事项&的截止日期，然后回到我正在做的事情上，这是无价的。这是*牛逼的*在开会。我可以快速捕捉到我需要跟进的所有内容，而不会忘记谈话的内容。

我不只是把我的待办事项放在那里。我还输入了一些项目，比如“期待 Thor 的数据流图”，截止日期是明天。这是我的提醒，如果我拿不到我需要的图表，就去找索尔。

一旦它出现在我的待办事项清单上，我就会彻底理清思绪，回到我正在做的事情上。OmniFocus 记得我所有的任务和截止日期，所以我不需要这样做。

总的来说，我是一个[做事](https://www.amazon.com/Getting-Things-Done-Stress-Free-Productivity/dp/0142000280)的人。使用任何对你有用的哲学/方法。如果你没有看过[兰迪·波许关于时间管理的演讲](https://www.youtube.com/watch?v=oTugjssqOT0)，停止阅读这篇文章，去看看吧。这段时间*会过得非常值得。*

**应用切换&自动化**

在这里，我喜欢阿尔弗雷德，但也有同样好的免费选择。(我也在其他东西上使用 Alfred，所以我选择支付许可费。我有，不如用吧。)

我有一套键盘快捷键，可以启动或切换特定的应用程序。打开我的浏览器。`Cmd-Shift-Ctrl-K`拉起我的 IDE。`Cmd-Shift-Ctrl-L`打开终端。比起 T3，我更喜欢 T5 和 T6。它让我完全通过肌肉记忆来切换应用程序。(另外:是的，我在 TextExpander 中将 **chordx** 绑定到了`Cmd-Shift-Ctrl-`。)

**大局**

最终，所有这些工具都是为了清除组织和精神上的混乱，这样你就可以集中注意力，而不是不断地被拉进杂草中。

当遇到任何阻碍时，有两种解决方法。一种是回避问题，让它变得无关紧要。另一个是变得非常善于处理它，以至于它不再是一个障碍，而是一个优势。

当我第一次开始上 CS 课的时候，我会听到同学们说这样的话，“为什么我们必须使用 cvs？为什么我们不能在网络驱动器上保存项目的副本呢？那应该没问题吧？”必须签入和签出文件，处理合并冲突等，感觉像一个障碍，他们试图回避它。

但是我们都知道拥有一个版本控制系统要好得多，以至于它从来没有在真正的软件团队中讨论过。正确的解决方案是善于使用 git/hg/svn，这样我们可以以最小的痛苦获得好处。

有效的编码建立在通用组织和交流技能的基础之上。如果你发现自己经常从高效的工作中抽身出来，花时间做家务，对着天空挥舞拳头是没有用的。将会有所帮助的是变得真正擅长做家务，这样它就不会再让你陷入困境。