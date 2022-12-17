# 用 OCaml 弄湿你的脚

> 原文：<https://dev.to/bobbypriambodo/getting-your-feet-wet-with-ocaml-dp8>

*最初发表于[媒体上](https://medium.com/@bobbypriambodo/getting-your-feet-wet-with-ocaml-ea1045b6efbc)*

我要坦白:我爱 OCaml。这是一见钟情，n 是一个足够小的正整数(一直推迟到有利于仙丹和哈斯克尔，但我已经开始后悔)。

OCaml 是静态的和强类型的，(大部分)功能性的，快速的，安全的，编译成本地，产生一个可执行的二进制文件。编译器非常快，它的 Hindley-Milner 类型系统(和 Haskell 的一样)非常复杂，你不需要过度注释你的程序，仍然是类型安全的。我一直在寻找一种快速编译成本地语言，最好是函数式语言，OCaml 似乎非常适合这个要求。

在大学里抨击 Java 多年，毕业后从事 JavaScript 一年，又从事 Java 几乎一年(karma？)，我不禁欣赏有显式类型的语言。编译器是一个非常棒、非常聪明的朋友，它实际上可以阻止你做一些愚蠢的事情，比如在代码中使用错误的类型。OCaml 的编译器更是如此。而且快得惊人！

所以我现在在这里，试着与你分享爱。但是首先，为什么是 OCaml？

### 理性的崛起

在我的 Twitter 上出现的一个趋势是 [ReasonML](https://reasonml.github.io/) 。Reason 是来自脸书的 OCaml 的语法和工具链。简单来说，这是 OCaml 的新面孔；与姐妹项目 [BuckleScript](https://bucklescript.github.io/) 一起，我们可以用 JS-ish 语法编写 OCaml，这种语法是类型安全的，可以针对 JS 和 native(甚至是 mobile！).很明显，它正在 Twitter sphere 和其他在线平台上获得牵引力。就连[丹](https://twitter.com/dan_abramov)也这么说:

> 我在列表时注意到一个有趣的模式:早期来到 [@reactjs](https://twitter.com/reactjs?ref_src=twsrc%5Etfw) 社区的人现在大多在推特上谈论 [@reasonml](https://twitter.com/reasonml?ref_src=twsrc%5Etfw) 。
> 
> — Dan Abramov ([@dan_abramov](https://dev.to/dan_abramov) ) [December 3, 2017](https://twitter.com/dan_abramov/status/937355817350828033?ref_src=twsrc%5Etfw)

丹的致谢！

随着任何技术的兴起，新的教程、文章和视频开始到处涌现。我也想做点贡献，但是我想从 OCaml 的角度进行报道可能会很有趣！有点遗憾的是，尽管 OCaml 不是一门新语言，但围绕它的资料却很少。我想改进这一点。(它正在稳步改善，毫无疑问，理性在吸引更多人加入生态系统方面做出了重大贡献。)

本文的目标是让您在自己的计算机上安装 OCaml，并实际尝试和探索一些东西。我不打算讨论语法之类的东西，只讨论如何设置开发环境。希望这可以帮助对 OCaml 感兴趣的初学者。如果你想了解更多，我还会在最后给你提供一些后续链接。

一些免责声明:你不会在这里得到太多的理由，但我相信，如果你想更深入地挖掘，这篇文章中的信息也可以让你受益。此外，有必要通知您，Windows story 在 OCaml 中的表现有点不足，而且有些部分已经损坏，所以我必须假设您运行在 Linux、MacOS 或 VM 上。

那么，让我们开始吧！

## 安装

有趣的东西:要安装 OCaml，你不需要安装 OCaml！(嗯，至少不是马上。)

你首先需要的是 OCaml 包管理器 [opam](http://opam.ocaml.org/) 。如果你来自其他语言，opam 差不多就是*Rust up+cargo*(Rust)；*nvm+NPM*(node . js)；*rbenv/rvm+gem*(Ruby)；*栈*(Haskell)；*pyenv+pip*(Python)；其他人也一样。基本上既是 OCaml 版本管理器*又是*包管理器。

我将带您安装 opam v2 的测试版(在撰写本文时是 2.0.0~beta5)。目前的官方稳定版是 v1.2.2，但是我用了几个月的 v2 beta，感觉已经足够稳定日常使用了。

要安装，打开您的终端并运行:

```
$ wget https://raw.github.com/ocaml/opam/master/shell/install.sh
$ chmod +x install.sh
$ ./install.sh --fresh 
```

Enter fullscreen mode Exit fullscreen mode

这里我们做三个步骤:

1.  从 repo 下载安装脚本
2.  使脚本可执行
3.  运行脚本(在运行之前，您可以先检查`install.sh`的内容)。

您也可以使用一行程序:`wget https://raw.github.com/ocaml/opam/master/shell/install.sh -O - | sh -s -- --fresh`。注意，一般来说，从互联网上运行一个 shell 脚本应该小心谨慎，因此我建议先检查一下内容。

该脚本将从与您的架构匹配的 [GitHub 发布页面](https://github.com/ocaml/opam/releases)下载预编译的 opam 二进制文件(大约 5 MB 大小)。它目前有 Linux (i686、arm64、armhf)、OpenBSD (amd64)和 MacOS/OSX (x86_64)的二进制文件。如果您已经安装了 opam，它会对您的旧二进制文件和`.opam`目录进行备份，所以它是安全的！

下载后，它会问你二进制放在哪里。除非你有特殊的理由，否则默认的`/usr/local/bin`就可以了。确保无论目标目录是什么，它在您的$PATH 中都是可用的。

通过运行`opam --version` :
来验证安装

```
$ opam --version
2.0.0~beta5 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们已经启动并运行 opam。如果您愿意，现在可以删除`install.sh`，因为我们不再需要它了。

接下来，我们将初始化 opam 环境。运行这个:

```
$ opam init 
```

Enter fullscreen mode Exit fullscreen mode

这将:

1.  检查计算机上已安装和可用的版本控制(例如 git、darcs、mercurial)
2.  获取 opam 存储库信息(基本上是列出 OCaml 第三方包的地方)
3.  提示您向您的`.bashrc` / `.zshrc` / `.*rc`文件添加一个条目来设置 opam 环境。缺省值是 no (n)，但是您很可能想说 yes (y)。这将使你在每次打开终端时不必执行额外的命令。
4.  为你安装 OCaml！

在撰写本文时，最新稳定的 OCaml 版本是 4.06.0，所以这是我在步骤 4 中得到的。在安装时，这可能需要很长时间，取决于你的计算机规格(特别是命令`make world`和`make world.opt`),但这没关系。这是因为我们正在从源代码编译 OCaml 二进制文件。最好习惯这一点，因为它偶尔会发生，例如，当我们想要切换 OCaml 版本或当我们想要开始破解一个最近创建/克隆的项目时。

(注意:opam 相关文件被隔离在`~/.opam`目录中，所以如果您想卸载它，只需删除该目录，删除上面步骤 3 中添加到您的`.*rc`文件中的条目，并删除 opam 二进制文件本身。)

之后，您可以使用`ocaml --version` :
来验证 OCaml 的安装

```
$ ocaml --version
The OCaml toplevel, version 4.06.0 
```

Enter fullscreen mode Exit fullscreen mode

整洁！我们现在可以通过调用`ocaml` :
来尝试内置的 REPL，官方称为“顶层”

```
$ ocaml
       OCaml version 4.06.0

# 
```

Enter fullscreen mode Exit fullscreen mode

`#`标志是一个提示。我们可以用它来做一个简单的计算器，就像这样(注意:双分号`;;`用来标记一个表达式的结束):

```
$ ocaml
       OCaml version 4.06.0

# 1 + 1;;
- : int = 2
# 2 + 10 * 3;;
- : int = 32
# "hello " ^ "world!"
- : string = "hello world!" 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你。您的 OCaml 安装工作正常。

## 切换 OCaml 版本

您将不时执行的一个操作是切换 OCaml 版本。正如我上面所描述的，opam 也是一个版本管理器；这意味着您的计算机上可以有多个 OCaml 编译器版本，并且能够在它们之间随意切换。

你也可以有版本别名，比如你想有两个别名`project-a`和`project-b`，每个都使用相同的 OCaml 版本。这两个被认为是两个不同的“开关”，彼此完全隔离。这对于依赖项隔离来说尤其重要——OCaml 希望包是全局可用的，可以想象如果我们对所有项目的依赖项使用一个全局名称空间，会导致什么样的版本冲突问题。(opam v2 上还有一个整洁的新特性，叫做*本地交换机*，但我们在本文中不打算介绍它。)

现在您已经安装了最新的 4.06.0，让我们切换到稍微旧一点的版本。一个很好的用例是 4.06.0 最近刚刚发布，并引入了一个(必要的)突破性变化，这导致了许多库无法构建(更多信息请参见这里的)。所以，我们换个老一点的稳定版:4.05.0！运行这个命令:

```
$ opam switch create 4.05.0 
```

Enter fullscreen mode Exit fullscreen mode

这将再次下载 4.05.0 编译器的源代码并在本地编译它。

是的，这需要一些时间。最好去拿你的咖啡！)

完成后，它会公布一个您需要运行的命令。让我们现在运行它:

```
$ eval $(opam env) 
```

Enter fullscreen mode Exit fullscreen mode

这将设置必要的环境变量，以正确指向新安装的交换机。要列出已安装的交换机，我们可以运行`opam switch` :

```
$ opam switch
#   switch   compiler                    description
->  4.05.0   ocaml-base-compiler.4.05.0  4.05.0
    default  ocaml-base-compiler.4.06.0  default 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，我们现在使用的是 4.05.0 交换机。你可能也注意到了 4.06.0 的名字是`default`。可以轻松切换回:

```
$ opam switch default
# Run eval $(opam env) to update the current shell environment
$ eval $(opam env)
$ opam switch
#   switch   compiler                    description
    4.05.0   ocaml-base-compiler.4.05.0  4.05.0
->  default  ocaml-base-compiler.4.06.0  default 
```

Enter fullscreen mode Exit fullscreen mode

不错！在，呃，开关之间切换，太快了。您可以使用`opam switch create <name> <compiler>`创建开关别名，例如`opam switch create project-a 4.05.0`，这将创建一个使用 4.05.0 作为基础编译器的`project-a`开关。请注意，该命令将再次在本地重建编译器，即使您已经安装了 4.05.0 开关！

(如果您使用的是 ReasonML 和/或 BuckleScript，这是您可以使用`4.02.3+buckle-master`作为编译器来获得兼容版本的地方。)

## 编写、编译和运行程序

好吧！现在我们将尝试编写一个简单的程序，将其编译成本机可执行文件，然后运行它。确保切换回 4.05.0 以进行本实验(`opam switch 4.05.0 && eval $(opam env)`)。

让我们来看看您的日常简单程序，它打印“Hello，OCaml！”到标准输出。打开你最喜欢的文本编辑器，写下这个:

```
let () =
  print_endline "Hello, OCaml!" 
```

Enter fullscreen mode Exit fullscreen mode

另存为`hello.ml`。我们有了源代码，现在让我们编译它:

```
$ ocamlopt hello.ml -o hello 
```

Enter fullscreen mode Exit fullscreen mode

这将把`hello.ml`编译成名为`hello`的本地可执行文件。现在让我们试着运行它！

```
$ ./hello
Hello, OCaml! 
```

Enter fullscreen mode Exit fullscreen mode

效果非常好！如果我们引入类型错误呢？让我们用这个程序试试:

```
let add a b = a + b

let c = add 1 "not a number" 
```

Enter fullscreen mode Exit fullscreen mode

另存为`should_error.ml`。当你试图编译它的时候会发生什么？

```
$ ocamlopt should_error.ml -o should_error
 This expression has type string but an expression was expected of type
         int 
```

Enter fullscreen mode Exit fullscreen mode

在 OCaml 中，不能“+”一个整数和一个字符串，因为`+`操作符只对整数进行操作，所以会导致编译错误！谢天谢地，没有奇怪的结果，如`1not a number`或`NaN`。

您可以使用该文件编写更多的 OCaml 代码，编译并运行它。

但是，请注意，您很少会直接使用`ocamlopt`来编译具有多个文件和复杂目录结构的程序，这些文件和目录结构使用第三方的包，因为您需要枚举和连接*您的程序所依赖的所有*文件和包。幸运的是，有几个工具可以为我们简化这一点，并且社区的大多数人都将 Jane Street 的 [jbuilder](https://github.com/janestreet/jbuilder) 作为事实上的构建系统。

我不会深入探讨构建系统的主题，也许这是另一篇文章的主题。只要记住你会得到很好的照顾！:)

## 使用 opam 安装库和程序

在这篇文章中我想和你分享的最后一件事是我们如何通过 opam 安装第三方库和程序。让我们来区分这两者:

1.  *库*是打算在代码中以编程方式使用的包；在…期间
2.  *程序*是提供您可以运行的可执行文件的包，例如通过命令行。

包可以充当程序和库，也就是说，它们提供了命令行可执行文件和 API 来以编程方式使用它。请注意，“程序”和“库”是我出于解释目的自己想出来的术语，我不确定是否已经有这样的约定。

从 opam 的角度来看，两者没有显著差异；只是程序通常在下载源代码后有额外的步骤，即复制生成的可执行二进制文件。使用`opam install`命令安装程序和库的包。

让我们试着安装一个程序，`ocp-indent`。这是一个实用程序，用来格式化你的 OCaml 源代码，以确保它有适当的缩进格式(非常有用！).运行此命令:

```
$ opam install ocp-indent
The following actions will be performed:
  - install result     1.2          [required by cmdliner]
  ...snip 8<...
===== 9 to install =====
Do you want to continue ? [Y/n] 
```

Enter fullscreen mode Exit fullscreen mode

它将列出要安装的软件包。请注意，我们将安装其中的 9 个，因为`ocp-indent`依赖于其他 8 个包才能工作 opam 为我们自动解决了它们！回答`Y`(或直接回车)继续安装。

首先，opam 将下载源代码(后续安装将从本地缓存中获取它们)。然后，opam 将构建所有的包。如果你注意到，每个包可能使用不同的命令来构建，例如`make`、`jbuilder build`等。这是因为每个包都提供了自己的构建指令。这样，库的作者可以灵活地选择他们使用的构建系统。

该过程完成后，您将安装`ocp-indent`程序。我们试试吧！

```
$ ocp-indent --version
1.6.1
$ ocp-indent hello.ml
let () =
  print_endline "Hello, OCaml!" 
```

Enter fullscreen mode Exit fullscreen mode

太好了！你可以尝试在`print_endline`前添加更多的空格，然后在`hello.ml`上再次运行`ocp-indent`，它应该会打印出带有正确缩进的程序。要实际修改文件，您可以提供`-i`标志(或`--inplace`)，例如`ocp-indent hello.ml -i`。

所以那是一个程序！现在，让我们试着安装一个库包。我们将安装一个轻量级测试框架`alcotest`。

```
$ opam install alcotest
The following actions will be performed:
  - install astring  0.8.3      [required by alcotest]
  ...snip 8<...
===== 5 to install =====
Do you want to continue ? [Y/n] 
```

Enter fullscreen mode Exit fullscreen mode

这次它将安装 5 个软件包。让我们继续进行`Y`。它将再次下载并构建源代码。

做完后，我们试试怎么样？

```
$ alcotest
zsh: command not found: alcotest 
```

Enter fullscreen mode Exit fullscreen mode

啊哦，发生什么事了？原来`alcotest`是一个库，因此它不提供任何命令行可执行文件。Alcotest 意味着从代码中使用。当然，我们现在不会讨论如何使用它；我只是想让你看看程序和库有什么不同。

好的，那么如何列出我们已经安装的软件包呢？我们可以用`opam list`。现在就试试吧！它将列出所有已安装的软件包。你也可以使用`opam list --installed-root`来查看你*直接*安装的包，去掉传递依赖。

软件包安装位于当前交换机的本地。如果你切换到另一个编译器开关，做`opam list`，你会看到上一个开关上的安装包不见了！同样，因为软件包被期望“全局地”安装，这样做是为了使每个交换机可以对它们安装的软件包进行隔离，并且项目不需要担心版本冲突。

我希望您现在已经掌握了使用 opam 安装软件包的要点！这也结束了我对 OCaml 生态系统的介绍之旅。

你的脚够湿吗？

## 那么，接下来呢？

在本文中，我们安装了 opam，安装了 OCaml 编译器，学习了如何切换编译器版本，如何编译和运行 OCaml 程序，以及如何安装第三方包。从这里去哪里？

对于初学者，你可以探索官方的[教程页面](http://ocaml.org/learn/tutorials/)。我建议浏览一下[的基础](http://ocaml.org/learn/tutorials/basics.html#Comments)，阅读一下 OCaml 程序的[结构](http://ocaml.org/learn/tutorials/structure_of_ocaml_programs.html)，然后按照你自己的步调循序渐进地完成剩下的部分。它会让你对这门语言的能力有一个很好的了解。

大多数人推荐的一个权威资源是[现实世界 OCaml](https://dev.realworldocaml.org/) 书，可以在网上免费获得。 [OCaml 从一开始](http://ocaml-book.com/)也是一本作为切入点的推荐书籍。

我计划在 OCaml 上写更多的文章。我在笔记中记下了两个，没有特别的顺序:为 OCaml 开发设置您的文本编辑器环境，以及用 jbuilder 和 topkg 构建和发布 OCaml 包。不过，我还不能保证什么！:D

我还写了一篇关于使用 OCaml 的多阶段构建来构建轻量级 Docker 映像的文章，可从这里获得:[具有多阶段构建的轻量级 OCaml Docker 映像](https://medium.com/@bobbypriambodo/lightweight-ocaml-docker-images-with-multi-stage-builds-f7a060c7fce4)。在编写它的时候，我使用的是 opam v1.2.2，但是根据本文的知识，只需要将 opam 命令翻译成 v2 的命令即可(唯一的区别是编译器的切换，这一点我已经在上面展示过了)。

被屏蔽？有问题吗？你可以尝试的几个沟通渠道:[discuss.ocaml.org](https://discuss.ocaml.org/)官方话语论坛、 [ReasonML Discord](https://discord.gg/reasonml) 、Reddit 上的 [/r/ocaml](https://www.reddit.com/r/ocaml/) 以及 Freenode 上的#ocaml。这个社区充满了友好的人们，他们非常乐意帮助你。

伙计们，今天就到这里吧！感谢阅读，我希望你能从这篇文章中有所收获！如果我能改进什么，请在评论中告诉我。