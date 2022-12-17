# 酏剂可执行文件概述

> 原文：<https://dev.to/roperzh/overview-of-elixir-executables-4bk>

*本帖最初发表于[monades.roperzh.com](https://monades.roperzh.com/overview-elixir-executables-escripts//?cm=devto)T3】*

Escripts 是分发自包含应用程序/脚本的好方法，任何用户都可以从命令行执行这些应用程序/脚本，唯一的要求就是安装 Erlang。

这里描述的大部分内容对于 Erlang 也是有效的(毕竟 escript 是 Erlang 的一个特性)，但是本文主要从 Elixir 的角度讨论 escript。

## Escripts

在这种情况下，构建可执行文件意味着将整个项目捆绑到一个具有任意扩展名的文件中，该文件可以从命令行调用并接受参数。Erlang 通过 [escripts](http://erlang.org/doc/man/escript.html) 提供了开箱即用的功能，而 Elixir 利用了这一点。

Escripts 允许您将整个 OTP 应用程序，*包括它的依赖项*打包到一个可执行文件中，每次 escript 启动时，都会触发一个独立的 Erlang 节点，因此您在 Erlang 中(以及在 Elixir 上)可以做的一切都可以在 escripts 中完成。

### 档案上的几个字

您可能会发现支持全局安装选项并通过[档案库](https://hexdocs.pm/mix/Mix.Tasks.Archive.html)在命令行中执行命令的 Elixir 库(一个著名的例子是[凤凰](http://phoenixframework.org/))，因此有必要澄清什么是档案库，以及为什么您应该尽可能地坚持使用 escripts。

Erlang 档案是一个扩展名为`.ez`的 zip 文件。该文件可以包含应用程序或部分应用程序以及其编译的 BEAM 文件*，但不包括应用程序依赖关系*。

在 Elixir 中，存档与 Mix 一起安装，它们有几个特点:

*   它们污染了系统中的每个混合项目:归档中的模块可以被其他应用程序访问。
*   存档的消费者必须安装 Elixir 来运行它们。

## 使用 Mix 管理脚本

Mix 附带了一系列内置任务来帮助你构建、管理和分发脚本，这些在官方文档中有很好的记录。以下是最常用命令的摘要:

**mix escript**

列出你已经安装的脚本，它只是一个目录列表`~/.mix/escripts/`

**mix escript.build**

编译项目及其依赖项，并将其打包在一个脚本中。在调用`mix escript.build`之前，只需要在你的`mix.exs`文件中定义一个带有`:main_module`选项的`:escript`键:

```
escript: [main_module: MyApp.CLI] 
```

Enter fullscreen mode Exit fullscreen mode

**混合描述安装**

如果没有提供参数，将尝试在当前文件夹中安装项目，否则，参数可以是本地路径或指向预构建的 escript、Git 存储库(`git <url>`)、GitHub 存储库(`github <url>`)或十六进制包(`hex <package_name>`)的 URL。

安装一个 escript 意味着编译并把二进制文件放在`~/.mix/escripts`中。

## 链接

*   [说明书](https://hexdocs.pm/mix/Mix.Tasks.Escript.html)的酏剂文档
*   用于[描述](http://erlang.org/doc/man/escript.html)的 Erlang 文档
*   2011 年，杰夫在二郎工厂不能说话的视频和[幻灯片](http://www.erlang-factory.com/upload/presentations/350/Escript-GeoffCantErlFSF2011.pdf)