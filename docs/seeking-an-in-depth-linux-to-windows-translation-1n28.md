# 寻求深入的 Linux 到 Windows 的翻译

> 原文：<https://dev.to/jacoby/seeking-an-in-depth-linux-to-windows-translation-1n28>

在工作中，我的桌子上有两台电脑。一般来说，左边的运行 Ubuntu Linux 并打开了许多编辑器和终端窗口，右边的运行 windows 并打开了浏览器窗口，因为使用我的工具的人运行 Windows 电脑和 Firefox。

我已经学会了最少量的 Windows 开发。我有一个 [C#项目来设置一个 JPG 作为我的背景图像](https://github.com/jacoby/SetBackgroundImage)，并且已经找到并稍微修改了允许我从 PowerShell 锁定、关闭和重启计算机的代码。当然，不多，但是作为一个 90 年代的 Linux 人，我属于可以随便使用“Micro$oft”的人群，并且会说“Andy giving 和 Bill Taketh Away”之类的话。

但 2018 年的微软已经不是 1998 年的微软了，我信任的许多人(包括两个现实生活中的朋友)已经去为新的微软工作了。他们有 Revolution R，他们有 Visual Studio 代码。他们有 Windows 上运行的 Bash 和 Ubuntu 的 userland，Linux 上运行的 Powershell。我过去运行 Windows *仅仅是为了给我的同事提供帮助，并获得流行的玩具(大部分是音乐和视频流媒体服务，还有一些游戏)。我现在想试着让*真的*使用它。*

这给我们带来了一个痛点。在 Linux 上，我有许多通过`crontab`安排的任务，确定是否有状态变化需要通知我，并使用`notify-osd`告诉我。什么样的事情？通常，邮件来自我关心的人，如老板、同事和家人。我讨厌自动列表中的随机邮件通知。还包括即将到来的风暴，我们的网络服务器或文件服务器的状态变化，甚至是我们已经到达了一个小时的顶点。(我*喜欢*的心流状态，但是当你抬头看时，想着*现在肯定*是午餐时间，发现已经是下午 6:23，你太脱离你的环境了。)

什么样的需要被提醒并不重要，只是由我来决定它是什么，并在 Windows 10 上运行。

这将我们引向已知的未知和未知的未知。已知的未知是我有一些感觉的东西，但不一定了解它们将如何工作。我确信在栈溢出上做一点搜索就能让我到达我想去的地方。这包括 XML 或 JSON 解析器、HTTP 客户端动作，以及某种程度上的 **TOAST** 。TOAST，我知道，是将东西发送到动作中心的机制，我发现了*用来*创建弹出部分的 PowerShell 代码，但是没有添加到动作中心。它不再起作用了，我不知道是不是我做了什么。

在转向 Windows 配置方式时，除了我需要了解的所有事情之外，还有未知的事情，那就是时间安排。我习惯加

```
0 * * * * /path/to/my/program --quiet 
```

Enter fullscreen mode Exit fullscreen mode

作为安排大多数事情的方式。我知道这个任务涉及到调度，但是我不知道使用 Admin 特权来访问调度程序是否是 Windows 方式。

我和许多 Windows 开发人员都不太合群，而我认识的少数几个人，我认为我对这个问题的了解还不足以给出有意义的答案。而且它的形式过于自由，不可能是一个堆栈溢出问题。

这是很高级的；我不需要代码示例。适当的关键字和链接到 MSDN 网页应该做我很好。我*知道*如何编程；我只是不知道如何在 Windows 中做事。非常感谢任何帮助。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。