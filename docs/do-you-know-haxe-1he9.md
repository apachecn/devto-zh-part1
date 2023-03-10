# 你知道哈克斯吗

> 原文：<https://dev.to/khaled_garbaya/do-you-know-haxe-1he9>

自从 Haxe 在 2006 年问世以来，已经有了很多变化，包括语言特性、平台支持等等。在这篇文章中，我们将尝试涵盖 haxe 的所有方面和变化，让您对该技术有一个全新的认识，我将继续撰写关于 haxe 入门的文章。

## 你知道哈克斯吗？

| 版本 | 出厂日期 | 主要特点 |
| --- | --- | --- |
| Haxe 1(r1) | 2006 年 8 月 | 将 Flash AVM2 目标与 haxelib-tool 一起添加 |
| Haxe 1(r2) | 2007 年 3 月 | 添加 Actionscript 3 目标和较小的错误修复 |
| Haxe 2.0 | 2008 年 7 月 | 由 Franco Ponticelli 提供添加 PHP 目标 |
| Haxe 2.04 | 2009 年 7 月 | 多亏了 Hugh Sanderson 添加了 C++目标 |
| Haxe 2.07 | 2011 年 1 月 | 添加对宏的支持 |
| Haxe 2.08 | Two thousand and eleven | javascript 目标的改进 |
| Haxe 2.09 | Two thousand and eleven | javascript 目标的改进 |
| Haxe 2.10 | 2012 年 7 月 | 添加 Java 和 C#目标，并为 Haxe 3 做准备 |
| 斧子 3 | 2013 年 5 月 | Haxe 3 在新成立的 haaxe 基金会下发布 |

## 编译器

编译器是用 OCaml 写的。它可以在服务器模式下运行，为 ide 提供代码完成并维护一个缓存，从而进一步加快编译速度。我和 haxe 一起工作已经有一段时间了，当我编译一个 haxe 项目的时候，我有时会有这样一个问题:“它在做编译器做的事情吗？”。Haxe 编译器如此之快，以至于你甚至无法注意到它正在编译，你告诉它为特定平台生成/运行，然后“baam！”你准备好了。

Haxe 编译器被分成一个前端和多个后端。前端负责对输入语言进行解析和类型检查，应用宏、常规优化、各种转换，并生成代码的中间表示，即类型化抽象语法树(AST)。每个后端负责将 AST 翻译成各自目标的源代码或字节码。

但是性能呢？

这取决于目标性能，例如在 Actionscript 中，使用 Haxe 可以获得比使用 Adobe flex SDK 编译的版本更好的性能。此外，对于 IOS，我们已经运行了一些性能测试，因为 haxe 通过 C++瞄准了这个平台，我们使用 Haxe 获得了令人惊叹的结果，性能提高了 130%

## 平台支持

| 编码发生器 | 平台 | 使用 |
| --- | --- | --- |
| 动作脚本 3 | Adobe Flash Player 9+/Adobe AIR | 网络/移动/桌面 |
| java 描述语言 | HTML5 | 浏览器/桌面/移动 |
| C++(haxcpp) | 苹果 IOS 和安卓 | 移动的 |
| 服务器端编程语言（Professional Hypertext Preprocessor 的缩写） | 服务器端编程语言（Professional Hypertext Preprocessor 的缩写） | 计算机网络服务器 |
| 大蟒 | 计算机编程语言 | 服务器/浏览器 |
| Java 语言(一种计算机语言，尤用于创建网站) | Java 语言(一种计算机语言，尤用于创建网站) | 服务器/台式机 |
| C# | 。NET 框架 | 服务器/台式机/移动设备 |
| Neko | 内科夫姆 | 服务器/台式机 |

## 众所周知的位于 haxe 之上的库/实用程序/引擎

OpenFL 是基于 Haxe 之上的 Flash API 的开源实现，它使得以前的 actionscript 开发人员可以更容易地使用更强大、更具性能的语言来开发几乎所有的移动、桌面和 web 主流平台。

lime 是一个灵活的轻量级层，面向跨平台开发者。Lime 支持本机、Flash 和 HTML5 目标，统一支持窗口、输入、事件、音频、渲染上下文、网络访问和 OpenFL 实际使用的资产

tink_macro 是一个超级强大的宏工具包，可以给 haxe 语言添加一些糖，如果你想了解更多关于 haxe 的宏特性，我真的鼓励你去看看。

Haxepunk 是一个建立在 openfl 之上的开源框架，从 Flashpunk 移植而来，让你可以构建自己的游戏，并以任何平台为目标。

HaxeFlixel 是一个开源的 2D 游戏库，用 Haxe 语言编写，由 OpenFL 提供支持。

如果我错过了 Haxe 上面的重要库/引擎/工具，请告诉我，我会相应地更新文章

## 接下来是什么？

我正在写更多关于 haxe 的文章，包括获得它的一个实例，在一些主题上获得更多的技术和深度。我的意思是在这里查看 haxe 网站。请务必安装它，并使用它。