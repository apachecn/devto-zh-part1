# 处理在 linux/osx 上找不到的命令

> 原文：<https://dev.to/k4ml/dealing-with-command-not-found-on-linuxosx>

我们大多数熟悉 linux 或 osx 的人都知道 shell 程序使用的`PATH`环境变量来查找我们输入的命令在哪里。每次我们输入命令，比如`virtualemv`，得到的错误是:-

```
bash: virtualemv: command not found 
```

Enter fullscreen mode Exit fullscreen mode

我们将检查`PATH`是否设置正确。但是对于我们使用的不同种类语言的所有环境管理器——rbenv、rvm、pyenv、conda、nix 等，它们都向`PATH`变量添加或预先添加了一些路径，以发挥它们的神奇作用。过了一会儿一切都变得一团糟。

当我们不确定某个命令来自哪里时，通常我们会使用`which`命令:-

```
which virtualenv
/Users/kamal/Library/Python/3.6/bin/virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

`which`的问题是它是一个外部程序，它只在`PATH`中寻找命令，它并不真正代表 shell 将实际执行什么。当您在 shell 中键入命令时，内置的 shell 命令`type`或`hash`会更准确地报告实际执行了什么命令。

有时候你会得到一个神秘的`command not found`甚至`which`或者`type`返回一条路径的命令。这可能是因为该命令已经指向一个新的可执行文件，但是 shell 内部哈希仍然指向旧的可执行文件。如果使用`bash`，您可以键入`bash -r`来重新散列所有的命令缓存。

关于这个 [Stackoverflow 问题](https://stackoverflow.com/questions/592620/check-if-a-program-exists-from-a-bash-script)的更多信息。