# 防止 Emacs 在 C-g 上杀死“git commit”

> 原文：<https://dev.to/lepovirta/prevent-emacs-from-killing-git-commit-on-c-g-5gbl>

如果您使用 Emacs 和 Git，您可能已经注意到在输入提交消息时按 control + G ( `C-g`)会终止整个提交过程。以下是您可以解决这个问题的方法。

Emacs 中的`C-g`用于取消命令。它还向 Emacs 发送 SIGINT 信号。该信号还被传递给父进程 git，这导致 git 结束整个进程。

解决方法是阻止信号到达 git。一种方法是在 shell 脚本中调用 Emacs。Shells 通常会忽略 SIGINT 信号。下面是这样一个脚本的例子:

```
#!/bin/sh -i
/usr/bin/emacs -nw "$@" 
```

Enter fullscreen mode Exit fullscreen mode

这里，shell 脚本以交互模式启动，调用`/usr/bin/emacs`，并将脚本的所有参数传递给 Emacs。将脚本保存在您的`$PATH`中的某个地方(例如`$HOME/bin/emacs.sh`，并让 git 使用该脚本作为其编辑器:

```
$ git config --global core.editor emacs.sh 
```

Enter fullscreen mode Exit fullscreen mode

同样的解决方法也适用于 Mercurial。更改 hg 的编辑器:

```
$ hg config --config ui.editor emacs.sh 
```

Enter fullscreen mode Exit fullscreen mode

如果您想将该脚本用作所有命令行程序的编辑器，只需更改您的 shell 初始化文件中的`EDITOR`和`VISUAL`环境变量:

```
export EDITOR="emacs.sh"
export VISUAL="$EDITOR" 
```

Enter fullscreen mode Exit fullscreen mode

来源:

*   [http://www.cons.org/cracauer/sigint.html](http://www.cons.org/cracauer/sigint.html)