# 笔记软件-调查

> 原文：<https://dev.to/apastuhov/note-taking-software---investigation-cp4>

*最初发表于我的博客:[apastuhov.com](http://apastuhov.com/post/find-best-note-app/)T3】*

* * *

和其他任何人一样，我想整理一下我的思路。最好的方法是写笔记！有人喜欢用一个简单的笔记本，上面有纸和笔，但我是一名软件开发人员，我大部分时间都在笔记本/PC/手机附近度过，而且我不太擅长手写。:)

我连续几年使用 notes，并尝试了不同的应用程序来简化这个过程。因此，我想分享我关于笔记应用程序的经验，并提供一些建议，如果你正在寻找满足你需求的最佳应用程序。

而且我要警告你，那是我的主观看法，所有的想法都是基于我个人的需求。

## 标准定义

在这里，我想描述我选择用来定义最佳笔记应用程序的标准。

*   **离线访问**——我喜欢旅行，最重要的是我更喜欢在火车上写文字，这里没有互联网接入，也没有办法远程连接。
*   **从移动设备离线访问** -我将这一点与前一点分开，因为一些应用程序可以离线访问，但他们的移动版本只能在线访问。
*   **跨平台**——我每天使用 3 个不同的平台:Android、Linux 和 Windows。而且我需要有一个好的跨平台的 app，数据同步稳定。
*   **支持[markdown](https://en.wikipedia.org/wiki/Markdown)**——它是最好的纯文本格式语法的标记语言！
*   一些 markdown 引擎不支持表格或图像，实际上，这对我来说很奇怪，因为 markdown 的基本功能有一个规范。
*   **支持基本文件夹结构(至少 1 级深度)** -及时我发现我需要按文件夹(组)对笔记进行分组，标签在这里不起作用。
*   用户友好界面 - UI 应该清晰易懂，没有任何开销和无用的按钮/功能。

很高兴拥有:

*   **加密数据** -私密性很好，但我不会在笔记中保存密码或一些有价值的数据，所以对我来说不是太关键。
*   FOSS -贡献和改进应用程序将会非常棒。
*   **数学支持([乳胶](https://en.wikipedia.org/wiki/LaTeX)为例)**——只是因为我有时候需要。；)

## [谷歌保持](https://www.google.ru/keep/)

有很多人喜欢它，但当我开始使用它时，我发现它的界面并不像我想象的那样友好和结构化。对我来说感觉有点乱。我不喜欢那种字体大小取决于你在卡片上写的数量的面板。

[![Google Keep](img/c677f51577a74599fdb1a9c11a377e3c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5PvKjY3A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/googlekeep.png)

## [收纳盒纸](https://www.dropbox.com/paper)

这是足够新的，有一段时间，它在一瞬间变得太受欢迎。我想指出的是，它有很棒的编辑器，在 [Dropbox 纸张](https://www.dropbox.com/paper)中写作是我非常喜欢的事情，对我来说，这是最好的写作应用程序。

直到我发现文件之间的导航真的很糟糕。当您编辑文件 a 时，它不能移动到文件 B。

也是主页..-它显示了让我连接 Slack 的广告(但我的个人电子邮件中没有任何 Slack 连接)。我拒绝了它，但最终我又把广告拿了回来。主页只显示最近浏览过的文件，但是等等，我有一个结构化的数据，我不知道什么是“最新的”。因此，要打开某个目录中的文件，我需要点击 3 或 4 次，然后等待页面重新加载几次。

[![Dropbox Paper](img/b9412337e6fc6ec10b9b63f4e14c54fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3h-7AocQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/dropboxpaper.png)

不过，编辑还是不错的。

## [一注](https://www.onenote.com/)

微软有一个很好的替代大多数笔记应用程序的方法。它有大量的热键。而且我用了很久，但是只在 windows 和移动平台上好用。和大多数开发人员一样，我的工作使用的是 Linux 系统，而网页版的 OneNote 太慢了。所以我不能说它真的是跨平台的。但是如果你只使用 Windows 作为你的主要平台- [一个注意事项](https://www.onenote.com/)是一个很好的应用程序。

[![One Note](img/c2a12d49d8d6dfa99928ae57c266fe6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J7a51CZ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/onenote.png)

附:我喜欢 OneNote UI 被组织成一个简单的笔记本，上面有彩色书签。

## [Evernote](https://evernote.com/)

太复杂，太多我不需要的功能，还需要钱来支持 2 个以上的设备。如果付款不成问题..我仍然不需要笔记应用程序的下一个功能:

*   提醒
*   不同的字体系列，大小，颜色
*   在 LinkedIn/Twitter/脸书/电子邮件等网站上分享..(真的谁会在 facebook 或 twitter 上分享笔记？))
*   工作聊天...好吧，但我需要个人笔记
*   日历中的便笺
*   笔记演示
*   在 PDF 中搜索？没有。

[![Evernote](img/0552829305be52949736529f476dfc1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2bddcGng--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/evernote.png)

## [SimpleNote](https://simplenote.com/)

很长一段时间，我都是 SimpleNote 的忠实粉丝。这是一个很好的笔记应用程序，支持 markdown 和伟大的离线访问。但是随着时间的推移，我有了新的要求来整理一些笔记，保存图片和表格。这里我发现 [SimpleNote](https://simplenote.com/) 还有下一个问题:

*   没有文件夹结构
*   降价文件中没有内部图像或表格
*   网络和桌面视图没有用户友好的降价/预览切换器

[![SimpleNote](img/cce48801e72b0b6f5761eeaab39b9216.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OaTQxmIQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/simplenote.jpg)

## [Turtl](https://turtlapp.com/)

不错的和最小的应用程序，但我不知道..UI 和 UX 对我来说有点尴尬，我也不喜欢面板。当你在一个组中有 20 个笔记时，我不觉得应用程序对用户友好。它和 Google Keep 有同样的问题。

[![Turtl](img/d7df70681b2ad9e52e7def5452a617d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--69-FfyM5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/turtl.png)

## [标准注释](https://standardnotes.org/)

哦..有那么一瞬间，这个应用产生了令人惊叹的效果。首先——它是由一个[人](https://github.com/mobitar)建造的。那个人是学习的榜样。第二，它有很好的 UX +用户界面，并且是开源的。但是像减价这样的原始功能需要大量的资金。我不确定它是否会如我所愿。

从我的角度来看，我的草稿有几个标签，我不需要一个标签一个标签地分组笔记。

[![Standard Notes](img/b8d2e915018d32d8101cb7b24465192b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_-0_Gfj0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/standard-notes.png)

## [t1](#laverna)[熔岩](https://laverna.cc/)

我给了那个应用几次机会..但是每次我尝试的时候——我都有一些连接问题，通过 Dropbox 登录等等，所以网页版是好的，但是桌面对我来说不工作。

[![Laverna](img/61da158fa51f95bcbebae173f5efdc9f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--92CIBqAf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/laverna.png)

## 无单个 app

当我查看大量流行的笔记应用程序时，我发现它们都存在一些问题。我开始关注一些允许你将笔记保存在个人存储器上的应用程序(Dropbox、GDrive、custom server)，移动设备将连接到该存储器，下载、更新数据并上传回来。至少在桌面上，你可以使用任何你想要的编辑器。

我尝试了很多，甚至花钱买了一些移动应用，但都失败了，所有这些:

[![Mobile note-taking applications](img/5f254979a18a27aa01bf36de581854c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H5f3n1lC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nmfch7zk68gjalxmbh72.jpeg)

更多...

[![Mobile note-taking applications 2](img/e01be3535ae91aa47cce57e2d898e6d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8JZmU4EO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nssjzwzxuyae6lbykp3r.jpeg)

甚至更多，但我不想与截图垃圾邮件。:)

有些没用，有些有 bug，只能离线，等等。所以这是一个失败。

## 个人申请

有一段时间，我决定创建我的个人应用程序来做笔记，但随着时间的推移，我开始同时做几个项目，所以那个项目被冻结了。但它完成了我需要的基本部分。

[![Personal](img/29ca4c5383f846c8720c237d831cf691.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JG5C_h5A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/personalapp.png)

## [Boostnote](https://boostnote.io/)

最后但同样重要的是应用。几周前我发现了它..这完全符合我对最好的笔记应用程序的看法！！！它不跟踪任何私人数据。您可以选择任何您想要的数据存储。它有漂亮的设计，库是活的，每个新版本都比以前的版本好。此外，您可以将其分叉或贡献给开源。但是它也有一些问题。手机 app(至少安卓版)没用，但是只要开源了——任何人都可以贡献或者创造自己对手机 app 的愿景，让它变得更好。

[![Boostnote](img/9c87eddec4658c073f351354898b3ef2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f_SeBQny--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://apastuhov.cimg/noteapp/boostnote.png)

## 对照表

*   空-不支持开箱即用
*   `+` -支架/工件
*   不确定

|  | Google Keep | 收纳盒纸 | onenote | Evernote | 简单笔记 | 龟 | 标准注释 | 岩浆人 | Boostnote 注意 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 脱机桌面访问 | + |  | + |  | + | + | + | + | + |
| 离线移动访问 | + | + | + | + | + | + | + |  |  |
| 交叉平台 | + | + |  | + | + | + | + | + | + |
| 降价 |  | + |  | + | + | + |  | + | + |
| 桌子 |  | + | + | + |  | + |  | + | + |
| 附加图像 | + | + | + | + |  |  |  | + | + |
| 文件夹结构 |  | + | + | + |  | + |  | + | + |
| 用户界面 |  |  | + |  | + |  | + | + | + |
| 安全性 | ？ | ？ | ？ | ？ | ？ | + | + | + |  |
| 城河 |  |  |  |  |  | + | + | + | + |
| 数学 |  | + | + |  |  | + |  |  | + |
| 稳定的 | + |  | + | + | + | + | + |  | + |

根据个人要求做出选择！

## 总结

我发现大多数 time 的笔记应用都决定将自己的愿景从“为人们制造”转变为“为团队和公司制造”。这让我很难过。一切都在从 B2C 向 B2B 转移，这可能有利于业务，但不总是对最终用户有利。

如果你不同意我的观点，但仍然在寻找满足你需求的最佳应用程序，这里有一个链接列表，上面有很好的描述、标准和投票，这些帮助了我写那篇文章。

*   [https://www . slant . co/topics/697/~最佳跨平台笔记 app](https://www.slant.co/topics/697/%7Ebest-cross-platform-note-taking-app)
*   [https://alternativeto.net/software/evernote/](https://alternativeto.net/software/evernote/)
*   [https://www . slant . co/topics/6303/~ note-taking-apps-for-Linux](https://www.slant.co/topics/6303/%7Enote-taking-apps-for-linux)

别忘了在评论中告诉我:你在用什么笔记应用程序？

## 更新:

这里有一个列表，是那篇文章中没有列出的其他应用程序，但它们可能会让你感兴趣。

*   [iaWriter](https://ia.net/writer/) -移动应用确实不错，但是 PC 版只有 Mac 才有。反正它有稳定的同步与 Dropbox 或 GDrive 和离线移动模式，所以它可以与你的桌面上的任何文本编辑器一起使用。
*   [Typora](https://typora.io/) -足够好，功能达到，但没有手机应用。
*   来自 Chinna 的申请
*   很好的移动编辑器，但是 GDrive sync (Pro 功能)对我不起作用。

目前，我正在使用 **Boostnote** ，它不是最好的，但至少比大多数应用程序要好。但是也有一个值得考虑的地方——使用**的 iaWriter** 和简单的桌面 Dropbox 同步和自定义文本编辑器。

别忘了在评论中告诉我:你在用什么笔记应用程序？

## 更新二:

对于苹果(iOS 和 Mac)用户，这里是另一个列表:

*   [熊](http://www.bear-writer.com/) -几个家伙推荐了它，所以它应该在那个列表的最上面:)
*   对程序员来说，这似乎是一个很棒的应用程序
*   [苹果笔记](https://support.apple.com/en-us/HT205773) -默认苹果笔记

组织和保存笔记的非典型方式:

*   [Workflowy](https://workflowy.com/) -组织笔记的非标准方式！列表笔记本:)
*   [动画师](https://dynalist.io/) -看起来与上一个相似

*   [Zim Wiki](http://zim-wiki.org/)

*   [小百科](https://tiddlywiki.com/)

*   [废物箱](https://scrapbox.io/)

另一个发现是下一个应用:

[乔普林](http://joplin.cozic.net/) -有**终端客户端** -但是我没有测试！

此外，大多数来自 [DevTo](https://dev.to/apastuhov/note-taking-software---investigation-cp4) 社区的人使用 GIT 来保存和同步个人笔记。下面是如何做到这一点的简短描述:

*   使用 git hub/git lab/BitBucket/等工具将存储库保存在笔记中
*   使用任何桌面文本编辑器:Vim、Emacs、VSCode、SublimeText、Notepad 等和 GIT
*   使用一些移动应用程序来克隆存储库，编辑然后同步数据。对于 Android，我尝试了口袋 Git T1 和 T2 MGit T3，它们的效果和我预期的一样。

这种方法非常有趣，对开发人员来说是个不错的选择！

* * *

*最初发表于我的博客:[apastuhov.com](http://apastuhov.com/2017/11/28/find-best-note-app/)T3】*

订阅阅读更多文章！