# 论包装的重要性

> 原文：<https://dev.to/thelonelyghost/on-the-importance-of-packaging>

你有一个想法，想把它变成一点代码来实现它。你是做什么的？你打开你的 IDE/编辑器，也许你把它放在一个文件夹里，用一些默认的工具进行林挺和(如果需要的话)编译，然后你就可以让代码工作了。它很粗糙，很丑，但是很有效。

现在你有了一些东西，你想给它增加一个新的特性或者解决一个 bug。你是做什么的？你遵循同样的过程。如果您有兴趣，也许可以在项目的基础上初始化一个新的 git 存储库，但是分支并合并回 master 并没有什么实际意义。备份代码？可能是 Bitbucket 上的账户，或者 GitLab 上的什么。它们都有免费使用层和私有存储库，这对于公众来说显然不够好。

随着更多的特性被破解，没有测试和零文档(除了这里和那里的一个评论)，一切都保持 100%的私密性。也许你试着让它在一个数字海洋服务器上工作，你已经建立了一个浮动的工作空间，有一个持久的互联网连接。这很难，但现在它工作了。算是吧。不管怎样，总有一段时间吧。

您的代码组合仍然缺少很多。并不是所有的东西都是可见的，因为并不是所有的东西都完好无损，可以展示给任何人看。你有很多这样的小项目，它们充斥着你所知道的不好的实践，但是要找出正确的方法要容易得多。您对待您的开源代码贡献几乎就像一场舞台表演，在那里您已经完美地考虑了所有的事情。首先，您必须编写文档，这意味着您必须清楚地定义您设置来运行脚本的雪花服务器。这也意味着你必须写使用文档。自动化测试也是如此，其重要性因项目实现语言的社区而异。

这是我多年来处理任何数量的一次性脚本的过程。我私下里会有一个经常性的、小范围的项目，当我不再需要它们或者它们由于其他地方的变化而中断时(例如，由于网站更新，web scrapers 中断)，我会删除该项目并宣布完成。当然，我只能在工作面试时口头提到“是的，我在空闲时间做了类似的东西”，但无法证明这一点，因为这是一周前的事了，而且我没有保存工作。

我最近发现，有一种方法可以缓解这些成长的烦恼。

# 包装

如今，每一种(合法的)语言都有一个包管理策略。不熟悉包管理的概念？让我们讨论一下这个问题。

你有一个新项目，假设它现在是用 Python 写的。使用`pip`安装时，标准的包格式是明确定义的。它包括依赖项、包名、作者姓名和联系信息、依赖项的版本约束...许多信息是——也应该是——标准化的。在 python 中，这是在库的基础上的`setup.py`中一致定义的。

考虑以下存储库结构:

```
├── LICENSE.md
├── README.md
├── setup.py
└── my_project
    ├── __init__.py
    ├── core
    │   ├── admin_commands.py
    │   ├── inbox.py
    │   ├── initialize.py
    │   ├── mentions.py
    │   ├── posts.py
    │   ├── user_interaction.py
    │   └── validation.py
    ├── helpers
    │   ├── misc.py
    │   └── wiki.py
    ├── main.py
    └── strings
        ├── debug.py
        ├── posts.py
        ├── responses.py
        └── urls.py 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个名为`my_project`的项目，它应该是`setup.py`中的包名。Python 有类似 Java 的约定`import package.name.here`来映射到`package/name/here.py`文件系统结构。

# 自述

软件包总是有一个自述文件。如果你使用一个包生成器(像`bundle gem`)并且它生成一个 README，*总是*删除默认生成的描述(和其他 TODO 行)并插入你自己的。不确定你的项目是关于什么的？请注意它目前涵盖的内容。你可以以后再更新。这就是版本控制的要点。

在自述文件中，包含符合您实际情况的安装说明。您是否也需要安装本机操作系统依赖项？这些可能不太适合放在`setup.py`或`*.gemspec`文件中。在这种情况下，把它放在自述文件中。

很多时候，样板文件包含了很多噪音，这些噪音对于公开发布一个包来说可能是不必要的。以下是我制作任何包装的最低要求，无论是公开的还是私人的。我是一个健忘的人，所以两个月后再回到一个项目中，无论如何都需要一些时间来恢复状态。我喜欢用这个自述文件的要求列表来简化自己:

*   使用
*   安装程序(假设一个新安装的操作系统)
*   描述
*   (如果适用)本机平台依赖性的假设

# 依赖关系

软件包为运行一个编写好的软件提供了一个非常清晰的定义。多年来，我一直听说成功的项目包括一个非常狭窄定义的范围，最好的方法是通过狭窄定义的接口。

在编程中，您可能会看到一个初级开发人员正在研究一个神奇的方法，它接受各种形式的输入，并能够将其规范化。除非这是他们正在创建的方法/命令/原子单元的主要功能，否则更有经验的开发人员会意识到这很容易变成维护噩梦。依赖性跟踪也是如此。拥有一组明确定义的必需依赖项(而不是最终用户反复试验来安装所有东西)总是更好。

# 矫枉过正

例如，Python 需要一些引导来设置一个包。对我来说，它包括查找以前项目的`setup.py`文件，修改它以适应新项目，并创建一些默认结构，如包含版本变量(`__version__`)和作者变量(`__author__`)的`my_project/__init__.py`。它还涉及到用 unittest 和 shell 脚本设置自动化测试，以使自动化测试易于执行。

下面是我在创建包时的启发:

*   有没有*需要包管理器的*依赖项(例如`pip`、`npm`、`gem`)？
*   源代码需要分成多个文件吗？
*   我是否在用比完整的端到端测试更精细的粒度来测试我的工作？
*   我需要像命令行工具一样轻松地部署它吗？
*   我需要发布版本并拥有源代码的稳定、测试和开发版本吗？
*   我在用 git 跟踪代码变更吗？

如果我对这些问题中的任何一个*的回答是肯定的，我知道要把这个项目变成一个包。*

这意味着我...

*   创建一个新的 git 存储库(和私有远程，用于备份目的)
*   创建自述文件(最好是在 markdown 中)
*   在我安装或删除依赖项时，跟踪我安装或删除的依赖项
*   制定一个测试策略，以确保一切按预期运行(自动或手动)
*   创建一组方便的脚本，用于运行仅限于当前项目的可重复任务(例如，Makefile、Rakefile、bundler binstubs、shell 脚本)