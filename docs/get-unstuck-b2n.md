# 分开

> 原文：<https://dev.to/lefebvre/get-unstuck-b2n>

在 Xojo，我们希望每个人都[学习编程](https://blog.xojo.com/2017/06/26/learn-the-fundamentals-of-programming-with-xojo/)。但是即使 Xojo 易于使用，特别是当[与其他工具](https://blog.xojo.com/2017/05/25/making-a-cross-platform-desktop-app-comparing-xojo-and-visual-studio-for-mac/)相比时，编程仍然具有挑战性。在某些时候，每个人都会被困在某个地方。这里有一些提示，不管你使用什么编程语言，下次遇到困难时，可能会帮到你。

## 想想你期望代码做什么

这大概是最重要的。不要只是写(或者复制/粘贴)代码，然后盲目运行。当然，这是可行的，但是当你陷入困境时，你将很难理解为什么你的代码没有像你期望的那样工作。

所以要做的第一件事就是仔细阅读你的代码，理解你期望它会做什么。理解每一行和它应该引起的行为。不要假设任何事情。

通常这个看似简单的过程会让你突然想起一些事情，从而向你揭示问题所在。你也会学到很多东西。

## 将你的代码分成多行

让您的代码在一行中做多件事通常很方便。例如，这段 Xojo 代码做了 4 件不同的事情:

```
ValueField.Text = Str(Calculate(Val(InputField.Text))) 
```

Enter fullscreen mode Exit fullscreen mode

如果这段代码出现运行时错误，或者值不符合您的预期，那么您就很难弄清楚了。

相反，把它分成多行，像这样:

```
Dim inputText As String = InputField.Text
Dim calcResult As Double = Calculate(inputText)
Dim outputValue As String = Str(calcResult)
ValueField.Text = outputValue 
```

Enter fullscreen mode Exit fullscreen mode

特别是，当你使用调试器时，这也会帮助你。

## 使用调试器

说到这里，利用调试器来帮助你理解你的代码实际上在做什么。当您的代码没有像您预期的那样工作时，调试器可以帮助您准确地显示代码正在做什么，而不是您可能认为它应该做什么。您将能够一行一行地单步执行代码，并观察变量值的变化。

要使用 Xojo 调试器，只需设置一个断点(在代码编辑器的空白处点击)。您也可以使用 Xojo [Break](http://developer.xojo.com/break) 命令根据条件设置断点。

## 使用日志

有时候调试器并不是一个选项，比如当你在处理经常被调用的东西时，比如用于绘图或鼠标移动的 UI 事件。在这种情况下，您可以使用老式的日志技术。

每种语言都有某种将文本输出到系统日志的日志命令。通过 Xojo，您可以使用[系统。DebugLog](http://developer.xojo.com/system) 命令将信息输出到 IDE 中的[消息面板，您可以在应用程序运行时查看这些信息。](http://developer.xojo.com/userguide/find-errors-messages-panels%24messages)

## 休息一下

如果你就是想不出什么，那么有时候你只需要休息一下。如果你被困在一个思路上，无法打破一个模式，远离这个问题，然后带着一副全新的眼光回到这个问题上，会有很大的不同。你可能会发现解决方案马上出现在你面前。

## 向别人解释

摆脱困境的一个好方法是试着向别人解释这个情况。我已经记不清有多少人告诉我，他们在花时间给同事写了一封电子邮件或在论坛上发了一个帖子后，回答了自己的问题。在写帖子的过程中(或者在他们发完帖子后不久)，在任何人有机会回复之前，答案就来到了他们面前。

这有时被称为“[橡皮鸭调试](https://en.wikipedia.org/wiki/Rubber_duck_debugging)”。

## 减少你的代码

有时候你面前有太多太多的东西，让你不知所措。也许你的方法、设计或项目太大了，你已经失去了对它的理解。

所以，当所有这些都失败时，开始减少你的代码。把事情简化到最少，就像你期望的那样。然后开始一次添加一个东西，一路验证你的期望。最终你应该能够确定问题首先出现在哪里，这样你就可以专注于解决它。

我希望这些技巧中的一个能够在你下次陷入困境时帮到你。如果你有其他的技巧，请与社区分享。

这篇文章最初出现在 Xojo 的博客上，名为如何摆脱困境。