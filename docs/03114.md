# 面向 Docker 开发新手的 Bash 函数和别名

> 原文：<https://dev.to/argherna/bash-functions-and-aliases-for-the-beginning-docker-developer-d4>

## 我从哪里来

我工作时运行 MacOS，娱乐时运行 Ubuntu。Unix-y 的东西一直吸引着我，因为它具有可组合性和可扩展性。

我用 Bash。一直都是。它有很多特性，并且是可扩展的。我使用 Zsh 有一段时间了，但是过了一段时间后我又回到了 Bash，因为让我的 shell 与我使用的脚本语言相匹配对我来说很重要(我并没有搜索 Zsh 脚本)。

我们的团队最近开始了迁移到外部供应商进行托管的过程。我们部署策略的一部分包括将我们的服务和应用程序部署到 Docker 上，这是我在愤怒中开始学习的。有新的范例、命令、带开关的子命令和各种我以前从未想过的额外功能。我不得不比以往更多地考虑服务的部署和管理。DevOps 终于抓到我了。

在这篇帖子里，我不描述 Docker 是什么，如何建立一个形象等等。严格来说，这是关于设置一些有用的东西，以便快速、轻松地学习和运行基本的和(对我来说)最常运行的 Docker 命令。

## 为什么要这样做？

开发人员必须非常了解他们的工具。有时我们发现自己一遍又一遍地重复同样的事情。幸运的是，现代 shells 的一些特性允许我们将多个命令组合成易于记忆的助记符，这些助记符简短且易于输入。特别是 Bash，可以在您的配置文件中定义函数和别名，以减轻学习所有这些命令的认知负担。

我已经开始收集 Docker 的一些信息，并在这里与你分享。下面是我发现在使用 Docker 时有用的 Bash 函数和别名的集合(也就是说，当我阅读我的`.bash_history`并看到相同的命令运行超过 10 次时)。

### 高于一切，要意识到不一致性

我很快了解到`docker`和`docker-compose`很相似，但同时又非常不同。它们共享许多同名的子命令。但是请注意，名称相似的子命令不一定支持相同的选项。自从 Docker 风靡全球以来，它的发展一直令人兴奋，但这是以命令的一致性为代价的。

### 功能 vs 别名

Bash 中的函数和别名表面上是相似的；你为一个长命令做一个助记符。但是它们彼此之间有很大的不同，理解其中的原因很重要。参见[这里的](https://askubuntu.com/a/163895/1641)快速解释 Bash 函数和别名之间的区别。如果您曾经在定义别名和函数之间有过选择，Bash 手册说:

> 对于几乎所有的用途，shell 函数都比别名更受欢迎。

- [Bash 参考手册](https://www.gnu.org/software/bash/manual/html_node/Aliases.html)

### 命名函数和别名

对于我们开发人员来说，命名是世界上最难的事情。当我考虑如何命名一个 shell 函数时，我的清单是:

*   长度:必须短(< 20 个字符，如果可能)。
*   描述性的:必须简洁地描述所采取的行动。

下面的 Docker 函数都以字母`d`(或`dc`代表`docker-compose`命令)开头，后面是有时被缩短的子命令，再后面是任何其他信息。通常我用下划线连接复合词，但对于 Docker 函数我没有。

别名应该总是比函数名短。如果你愿意，你可以把它们变长，但是 90%的时候我会把它们变短。

### 功能参数检查

Shell 函数可以传递参数。我经常在我写的函数的开头做一个检查，看起来像这样:

```
 if [ $# -eq 0 ]; then echo 'Usage: $FUNCNAME ARG [ARG ...]'
  return 1
fi 
```

Enter fullscreen mode Exit fullscreen mode

`if`测试当然取决于函数需要什么。这个代码片段说“如果没有参数传递给函数，打印出一条用法消息并返回 1”。如果您在另一个函数或脚本中使用该函数，返回代码很重要。`$FUNCNAME`变量是一个内部 Bash 变量(参考下文),它打印出当前运行函数的名称。

## 我如何使用函数

### 外壳装入容器

有时事情并不像你预期的那样工作，你必须仔细观察你构建的容器，看看发生了什么。你需要用一个 shell 来做这件事(或者像我以前多次听到的，“shell into”容器)。Docker 的`exec`命令会运行一个命令。由于我想在一个指定的容器中启动一个 shell，下面的函数简化了我的工作:

```
# Run a bash shell in the specified container.
#
dexbash() {
  if [ $# -ne 1 ]; then echo "Usage: $FUNCNAME CONTAINER_ID"
    return 1
  fi docker exec -it $1 /bin/bash
}

alias deb='dexbash' 
```

Enter fullscreen mode Exit fullscreen mode

这应该理解为“docker exec bash”。`deb`别名将其缩短为一个漂亮的 3 个字母的调用。为什么不把函数命名为`deb`？你可以。这是你的壳，你可以自由地做你想做的事。

当你用`docker-compose`运行多个容器时，也有一个`exec`命令，但是一定要去掉`-it`选项。

```
# Run a bash shell in the specified container (with docker-compose).
#
dcexbash() {
  if [ $# -ne 1 ]; then echo "Usage: $FUNCNAME CONTAINER_ID"
    return 1
  fi docker-compose exec $1 /bin/bash
}

alias dceb='dcexbash' 
```

Enter fullscreen mode Exit fullscreen mode

一旦你进入，你可以寻找文件，运行探测命令等。就像在普通的 shell 中一样。当然，你最喜欢的一些工具将会丢失，因为这是一个 Docker 容器，它必须保持很小。

### 建筑和标注

有时候你想构建，有时候你想构建**和**标签。这是一个如何让你的 shell 函数变得智能的例子，并且演示了函数的别名:

```
# Runs Docker build and tag it with the given name.
#
dbt() {
  if [ $# -lt 1 ]; then echo "Usage $FUNCNAME DIRNAME [TAGNAME ...]"
    return 1
  fi ARGS="$1"
  shift if [ $# -ge 2 ]; then ARGS="$ARGS -t $@"
  fi docker build $ARGS
} 
```

Enter fullscreen mode Exit fullscreen mode

这将构建或构建并标记(使用多个标记)您的容器。

### 只是一些别名

请记住，别名只是带有一些标志的重命名命令。它们不像函数那样接受参数，但是它们会在命令行上附加您添加给它们的任何标志和参数。这些是我最常用的 Docker 命令的别名。

```
alias datt='docker attach'
alias dcb='docker-compose build'
alias dclogs='docker-compose logs'
alias dcu='docker-compose up'
alias ddiff='docker diff'
alias deb='dexbash'
alias dimg='docker images'
alias dins='docker inspect'
alias dps='docker ps'
alias drm='docker rm'
alias drmi='docker rmi'
alias drun='docker run'
alias dstart='docker start'
alias dstop='docker stop' 
```

Enter fullscreen mode Exit fullscreen mode

### 几个(更多)我喜欢的 s/东西/别名/

我注意到外壳函数比外壳别名更好，但是有时别名对工作来说是正确的。以这块宝石为例:

```
# Credit to <https://gist.github.com/bastman/5b57ddb3c11942094f8d0a97d461b430#remove-docker-images>
alias dclimg='docker rmi $(docker images --filter "dangling=true" -q --no-trunc)' 
```

Enter fullscreen mode Exit fullscreen mode

想象你正在处理一个 Docker 图像，并且你在一遍又一遍地构建它。作为初学者，您可能没有意识到的是，以前构建的容器不会简单地被覆盖！它仍然在你的硬盘上占用空间！当你运行一个`docker images`(或者上面的`dimg`，如果你注意到的话)你会看到没有名字的图像。这个小家伙将在一个命令中为你清理所有这些！

我经常做的另一件事是指定配置文件所在的卷，这样我就可以快速方便地编辑它们。根据我正在做的事情，我可能需要重启一个容器。下面这一个是对那个的美差:

```
# Credit to <https://stackoverflow.com/a/21928864/37776>
alias drestartf='docker start $(docker ps -ql) && docker attach $(docker ps -ql)' 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

Bash 显然是我最喜欢的 shell，不仅仅是别名和函数。其他 shells 也有别名和函数，这些都可以适用于它们。如果您在使用 Docker 时使用命令行，我希望这些对您有用。

## 码头工人&狂欢如何

*   [Bash 参考手册:Shell 函数](https://www.gnu.org/software/bash/manual/html_node/Shell-Functions.html)
*   [内部变量(Bash Shell)](http://tldp.org/LDP/abs/html/internalvariables.html)
*   [移除 Docker 容器和图像](https://zaiste.net/posts/removing_docker_containers/)