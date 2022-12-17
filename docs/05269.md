# JavaScript 林挺工具

> 原文：<https://dev.to/mert/javascript-lintingtools>

林挺静态地检查一个程序的源代码，找出不符合某种代码风格的代码，并发现代码中有问题的模式。作为一种动态的、松散类型的语言，JavaScript 从林挺工具中受益匪浅。

下面是最流行的 JavaScript 林挺工具，按照发布的顺序列出并简要解释。

欲了解更多信息，请访问项目自己的网站。

* * *

## [JSLint](http://www.jslint.com/)

JSLint 是道格拉斯·克洛克福特创建的一个工具，旨在通过对照道格拉斯·克洛克福特定义的规则检查代码来确保高质量的 JavaScript 代码，他在他的书《JavaScript:好的部分》中详细描述了这些规则。

尽管 JSLint 有几个选项可以“容忍某些事情”,但它不是一个可配置的林挺工具。原因在 jslint.com 上解释如下:

> JSLint 旨在拒绝一些人认为非常好的代码。这样做的原因是 JSLint 的目的是帮助生成没有错误的程序。这在任何语言中都是困难的，在 JavaScript 中尤其困难。JSLint 试图帮助你增加正确程序和错误程序之间的视觉距离，让剩下的错误更加明显。JSLint 将对在当前情况下不一定是错误的，但被观察到掩盖或掩盖错误的事情给出警告。当有更好的选择时，避免这些事情。
> 
> 外面很危险。JSLint 是来帮忙的。

您可以通过上面的链接在线使用它，也可以将其集成到您的开发和/或部署工作流中:

**命令行工具:**【https://github.com/reid/node-jslint】T2

**咕哝插件:**【https://www.npmjs.com/package/grunt-jslint】T2

**一饮而尽插件:**【https://www.npmjs.com/package/gulp-jslint】T2

GitHub 上也有源代码:

[https://github.com/douglascrockford/JSLint](https://github.com/douglascrockford/JSLint)

如果你希望对林挺标准有更多的控制，可以看看 JSHint，下面会有解释。

## [JSHint](http://jshint.com)

JSHint 是一个社区驱动的、灵活的、开源的林挺工具，它不试图强制执行某些编码偏好，并允许开发人员根据他们的编码约定来配置它。JSHint 只是根据给定的样式检查代码，并检测代码中的错误和潜在问题。

您可以通过上面的链接在线使用它，也可以将其集成到您的开发和/或部署工作流中:

*   **命令行工具:**【https://www.npmjs.com/package/jshint】T2
*   **咕哝插件:**【https://www.npmjs.com/package/grunt-contrib-jshint】T2
*   **一饮而尽插件:**【https://www.npmjs.com/package/gulp-jshint】T2
*   **更多信息:**【http://jshint.com/install/】T2

## [ESLint](https://eslint.org/)

ESLint 是下一代 linter，高度可配置，允许自定义解析器和插件扩展其林挺进程。ESLint 的可定制性应该不会让您不知所措，您仍然可以像使用 JSHint 一样简单地使用它。

为了充分理解 ESLint 是什么和不是什么，下面是 ESLint 背后的哲学，来自它的“关于”页面:

> 一切都是可插拔的:
> 
> *   捆绑规则和自定义规则都使用规则 API
> *   打包的格式化程序和自定义格式化程序都使用格式化程序 API
> *   可以在运行时指定附加规则和格式化程序
> *   规则和格式化程序不必捆绑在一起使用
> 
> 每条规则:
> 
> *   独立
> *   能够被关闭或打开(没有什么可以被视为“太重要而不能关闭”)
> *   可以单独设置为警告或错误
> 
> 此外:
> 
> *   规则是“无议程的”——ESLint 不提倡任何特定的编码风格
> *   任何捆绑的规则都是通用的

如需了解更多信息，请遵循[ESLint](https://eslint.org/docs/user-guide/getting-started)入门指南:

如果您正在寻找上述更高级的功能，请遵循关于[配置 ESLint](https://eslint.org/docs/user-guide/configuring) 的详细说明:

* * *

## 但是，选哪个呢？

在当今时代，ESLint 凭借其可扩展性和开箱即用的支持，无疑是满足您现代需求的最佳选择。

按照上面链接的基本说明开始使用 ESLint，并根据您的需求深入了解它的功能。

* * *

你正在使用林挺工具吗？在评论中分享你的经验吧！