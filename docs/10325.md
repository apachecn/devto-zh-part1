# Visual Studio 代码-提示和技巧

> 原文：<https://dev.to/stefanbc/visual-studio-code---tips--tricks>

[![Visual Studio Code - Tips & Tricks](img/2ae69bd4ce67b096e14025703370bde9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fp_bhZzu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://stefancosma.xyz/conteimg/2017/01/5.gif)

如果你在 Twitter 上关注我，你可能知道我有多喜欢使用微软的 Visual Studio 代码。以至于我的一条推文现在出现在他们的主页上。

但是这篇文章不是关于我有多喜欢我的代码编辑器，而是关于你能用这个代码编辑器做什么。从有用的扩展到您可能不知道的快捷方式，甚至是让您的代码编辑器看起来更亮，本文将展示我在使用它时发现的一些很棒的东西。

## 分机

我们以一些令人敬畏的扩展开始我们的展示。现在，我不打算分享我使用的所有扩展，因为它们中的一些确实是以项目为中心的，相反，我将分享一些更通用的扩展，它们使我作为开发人员的生活更加容易。

1.  [自动关闭标签](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag) -自动添加 HTML/XML 关闭标签。
2.  [git 历史(Git 日志)](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory) -查看 Git 日志、文件或行历史。
3.  [HTML CSS 类完成](https://marketplace.visualstudio.com/items?itemName=Zignd.html-css-class-completion) -根据工作区中的 CSS 文件为 HTML 类属性提供 CSS 类名完成。
4.  HTML 预览 -在编辑 HTML 或 Jade 文件时预览它们的扩展。
5.  [HTML 片段](https://marketplace.visualstudio.com/items?itemName=abusaidm.html-snippets) -完整的 HTML 标签，包括 HTML5 片段
6.  [JavaScript (ES6)代码片段](https://marketplace.visualstudio.com/items?itemName=xabikos.JavaScriptSnippets)-ES6 语法中 JavaScript 的代码片段。
7.  [JavaScript Atom 语法](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-atom-grammar)-Atom 编辑器的 JavaScript 文本匹配语法。
8.  [jQuery 代码片段](https://marketplace.visualstudio.com/items?itemName=donjayamanne.jquerysnippets) -超过 130 个 jQuery 代码片段
9.  jshint -将 jshint 集成到 VS 代码中。JSHint 是 JavaScript 的 linter。
10.  [设置同步](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync) -使用 Github Gist 在多台机器上同步设置、代码片段、启动、按键绑定、工作区和扩展。
11.  [拼写和语法检查器](https://marketplace.visualstudio.com/items?itemName=seanmcbreen.Spell) -使用网络服务来检测错误并提出修复建议-非常适合 Markdown 或任何文本文件。
12.  SVG 查看器 -非常简单明了。

我上面列出的扩展是我日常使用的一部分。如果让我来选择，没有它我就无法生活的是#1、#2、#6、#9、#10。

例如#10 是一个救生圈，因为我总是在我的工作 MB Pro 和我家里的 MB Air 之间切换，所以有一个帮助我同步设置的系统是教科书般的棒。

## 快捷键

接下来是快捷方式。我通常有一些我绝对离不开的捷径。例如`CTRL + D`对于多选是必须的。

当 VS 代码伴随着它的集成终端出现并为它引入快捷方式时，我就被迷住了。如果你像我一样，讨厌总是在代码编辑器和终端之间切换，那么你会喜欢这样。当我写代码时，我通常会避免使用触控板，这就是为什么我要分享一些快捷方式，在你编码的同时需要一个终端时，这些快捷方式会对你有所帮助。

1.  `CTRL +`` --使用此快捷方式，您可以切换集成终端。
2.  `CTRL + SHIFT +` ` --这个快捷方式将添加一个新的终端实例，所以你可以很容易地同时处理多个东西。
3.  但是如果您想在终端实例之间切换呢？我添加了一些键绑定，这样我可以很容易地在终端实例之间切换。当然，您可以根据自己的喜好定制它们。

``
[
{
"key":"ctrl+shift+=",
"command":"workbench.action.terminal.focusNext",
"when":"terminalFocus"
},
{
"key":"ctrl+shift+-",
"command":"workbench.action.terminal.focusPrevious",
"when":"terminalFocus"
}
]
``

1.  在最近的更新中，VS 代码背后的团队引入了一个新的特性。在侧边栏的不同视图之间切换的能力。使用这个快捷方式，你可以很容易地在浏览器视图或 Github 视图甚至搜索视图之间切换。
2.  举例来说，当我写代码时，我喜欢让事情尽可能简单。使用这个快捷方式，你可以很容易地显示或隐藏侧边栏。

我上面列出的快捷方式是我最常用的。有很多我甚至没有提到的，如果我提到了，就会忽略本文的整个目的。如果你知道更多，你认为真正有用的，让我知道，我会把它们添加到列表中。

## 杂项

由于我不能以谈论快捷方式来结束文章，这里有一些你可以在 VS 代码中做的更酷的事情。你知道你可以从视图菜单中隐藏活动栏吗？现在你知道了。这个功能是最近更新的，我可以说它非常有用，即使一个人在他/她的显示器上有足够的空间。

长相呢？就我而言，我更喜欢太空灰主题。自从我在使用 Sublime Text 2 时第一次发现这个主题，我就喜欢上了它。幸运的是，有人也将它用于 VS 代码。点击查看[。](https://marketplace.visualstudio.com/items?itemName=ionutvmi.spacegray-vscode)

最后，但肯定不是最不重要的，您知道您可以开箱比较文件吗？我最近需要比较同一个文件的两个不同版本，因为它们不在 Github 上，所以我需要另一种简单的方法来实现这一点。经过一些搜索和一些扩展之后，我偶然发现了一篇文章，这篇文章概述了如何在 VS 代码中直接做到这一点。因为我喜欢在该表扬的地方给予表扬，所以这里的[就是问题中的文章](http://dailydotnettips.com/2015/06/04/how-to-compare-files-in-visual-studio-code/)。

现在，不要误解我的意思，我知道我在这篇文章中分享的东西只是 VS 代码所能做的一小部分，这就是为什么我邀请所有使用 VS 代码的人分享他们的经验和技巧。

直到下一次，代码长，繁荣！

*图片来源:[纪尧姆·库尔克坚](http://guillaumekurkdjian.com/)T3】*

*本帖最初发表于[stefancosma . XYZ](https://stefancosma.xyz/2017/01/08/visual-studio-code-tips-tricks/)T3】*