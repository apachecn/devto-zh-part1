# 开发人员的生产力

> 原文：<https://dev.to/tiffany/productivity-for-devs-5g5>

在阅读 Practical Dev 寻找新帖子时，我看到了一个引起我兴趣的帖子:

[![sobolevn](img/e5348283b1b658d728d7128ee008a9af.png)](/sobolevn) [## 命令行效率提升 100%

### 尼基塔·索博列夫 8 月 23 日 174 分钟阅读

#productivity #dotfiles #commandline](/sobolevn/instant-100-command-line-productivity-boost)Productivity for devs is something I’ve been meaning to write about. Now I have the impetus to do so.

我认为安装 *oh-my-szh* 和使用*别名*让我成为一个坏蛋。不幸的是，事实并非如此。

这篇文章将谈论开发者的生产力，就像在主题为的 [Syntax.fm 节目中一样。](https://syntax.fm/show/011/our-favourite-productivity-hacks)

## 完成任务

我发现，首先也是最重要的是，一个伟大的任务经理对于记住你需要做什么是至关重要的。我使用 [OmniFocus](https://www.omnigroup.com/applications/omnifocus/) 作为我的主任务管理器，在我所有的设备上 <sup id="fnref-2459-1">[1](#fn-2459-1)</sup> 。OmniFocus 在 GTD 的尝试可能看起来很僵硬，但它非常灵活:使用你想要的。

[![](img/a5e27e2dbe425410127c00eb030a389a.png)T2】](https://d.pr/i/wP32oT+)

### Trello 进行项目管理

我用 Trello 来管理我的项目，它在这方面做得非常好。我需要对我需要做的事情有一个高层次和可视化的概述，每次我都会使用 Trello Mac 应用程序。

## 开发工具

对于我的 shell，我使用了第三次迭代的[iTerm 2](https://iterm2.com/)<sup id="fnref-2459-2">[2](#fn-2459-2)</sup>和`zsh` shell。

[Wes Bos](https://wesbos.com/) 有一个[伟大的主题](https://github.com/wesbos/Cobalt2-iterm)用于*所有的事情*，这是崇高文本钴 2 主题的一个分支，它是我在 iTerm 以及 [Visual Studio 代码](https://code.visualstudio.com/) <sup id="fnref-2459-3">[3](#fn-2459-3)</sup> 上使用的，虽然它在一个窗口中缺少多个项目，但比 Atom 快。

[![](img/2505dba9c26acfb48d4c148147312715.png)T2】](https://cdn.tiffanywhite.tech/wp-content/uploads/2017/09/30192424/Screenshot2017-09-30_04-41-56_PM.jpg)

有了主题和显示你的 git 状态和你所在目录的[电力线字体](https://github.com/powerline/fonts)，它就成为了一个很棒的终端。

[![](img/c7bde0392949bf8b73eb28ddfb9ee630.png)T2】](https://cdn.tiffanywhite.tech/wp-content/uploads/2017/09/30192412/Screenshot2017-09-30_04-39-30_PM.jpg)

iTerm 能够为您想要的任何内容创建单独的概要文件。例如，我有一个 Check Yo Self 配置文件，每当我想在那个特定的应用程序上工作时，我就可以切换到它。

[![](img/98d3ace2941be89a42acab4c7e598d67.png)T2】](https://cdn.tiffanywhite.tech/wp-content/uploads/2017/09/30192439/Screenshot2017-09-30_04-37-52_PM.jpg)

### 命令行工具

#### Zsh 语法高亮显示

知道您的别名或命令中是否有打字错误对于不把事情搞砸是至关重要的。告诉你正在输入什么的方法之一是使用[zsh-语法-高亮显示](https://github.com/zsh-users/zsh-syntax-highlighting)。你可以通过查看[荧光笔文档](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/docs/highlighters.md)来定制高光的颜色。

#### Z–四处跳跃

[z](https://github.com/rupa/z/) 是一个实用程序，允许你通过键入`z`然后目录跳转到不同的目录。你将不得不在目录中浏览一会儿来“训练”它，但是一旦你这样做了，在目录中跳过是相当容易的。这是一个“frecent”工具:频繁和最近的目录。

#### Exa: ls 替换

另一个很酷的命令行实用程序是 [exa](https://github.com/ogham/exa) ，它用一些漂亮的特性取代了`ls`，比如语法高亮和大量其他命令。只要输入`exa`，它就会列出你的目录的内容。你可以获得目录许可，用`-1`标志在一行上打印每个项目，等等。

[![](img/72e824043e72c29b0fcfc404e2cedb54.png)T2】](https://cdn.tiffanywhite.tech/wp-content/uploads/2017/09/30192448/Screenshot2017-09-30_06-28-20_PM.jpg)

## 杂项工具

### 阿尔弗雷德

我用阿尔弗雷德应用程序导航几乎所有的东西。工作流让我可以从文件中创建 gists，搜索堆栈溢出，将 Trello 卡添加到不同的板上，从 Chrome 标签中创建 OmniFocus 任务，等等。

<figure>[![Getting things done or...wasting time?](img/4120ff8fbb004171049e76a6aadf0261.png)](https://cdn.tiffanywhite.tech/wp-content/uploads/2017/09/30192459/Product_hunt.jpeg) 

<figcaption>把事情做完还是……浪费时间？</figcaption>

</figure>

我也用韦斯博斯的[钴 2 阿尔弗雷德主题](https://github.com/wesbos/Cobalt2-Alfred-Theme)来做这个。

### 去他妈的工作

[去他妈的工作](https://www.gofuckingwork.com/)是一个[的 Chrome 扩展](https://chrome.google.com/webstore/detail/go-fucking-work/hibmkkpfegfiinilnlabbfnjcopdiiig)。这和保持专注很相似，但是更加严格……顾名思义。

[![](img/a375c6be00de684dde3bcdb3494f262c.png)T2】](https://cdn.tiffanywhite.tech/wp-content/uploads/2017/09/30192859/Screenshot2017-09-30_06-36-55_PM.jpg)

当我和我认识的人在一起时，我像水手一样发誓，所以我很欣赏这种扩展及其可定制性。当然，如果你不喜欢瑞典的东西，还有其他选择。

### 自由

[![](img/88edaa5c2c6cab82ea3e966dd9f00aa6.png)T2】](https://cdn.thatmacnerd.com/wp-content/uploads/2017/11/25212721/freedom_mac-4.png)

自由是一个小小的 Mac、iOS 和 Windows 工具，可以阻止令人分心的网站和应用程序。

我不久前在 iOS 上下载了它，虽然它运行良好，但有时 VPN 配置文件仍然会阻止访问脸书等应用程序。真的是伪块。它会阻止应用程序刷新，但不会阻止你查看过去的更新。我卸载了它，因为我很擅长在工作时间打电话。这主要是在我的 Mac 上，我在那里工作，这是一个问题。

我之所以把它下载到我的 Mac 上，是为了能够屏蔽应用程序。我需要一种方法来停止打开我的文本编辑器，整夜处理问题和功能，这是不健康的。我已经屏蔽了我会用来做开发的应用程序，基本上是在应用程序启动时就退出，这样我就不会在下班后做任何事情。我在工作日做这个，因为如果我愿意，周末我可以做一些兼职项目。

## 结论

生产力很重要，你使用的工具应该适合你的工作方式。只要确保在你坚持工作的同时，你也保持了健康，并花时间照顾自己。

* * *

1.  目前，Omnigroup 只开发苹果应用程序，没有 OmniFocus 的网络界面。web 界面可能很快就会出现。在 Android 上，有一个应用程序使用 Omnigroup 的 API 与 OmniFocus 接口，名为 [Focus GTD](https://play.google.com/store/apps/details?id=com.burgstaller.android.focusgtd&hl=en) 。↩
2.  不应该叫 *iTerm 3* 吗？↩
3.  这是新的热点。当它第一次出现的时候，我试了一下，并且只使用它，但是因为插件的原因，我又回到了 Atom。现在每个人似乎都在谈论它，所以我又试了一次。↩