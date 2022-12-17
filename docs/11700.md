# 用家酿和 Dropbox 在 OSX 上设置 todo.txt CLI

> 原文：<https://dev.to/cwardzala/setup-todotxt-cli-on-osx-with-homebrew-and-dropbox-60i>

我最近重新发现了 [todo.txt](http://todotxt.com) 这个方便的纯文本待办事项列表，以及它的 CLI。我已经爱上了这种保存待办事项列表的方法，它可以添加标签，还具有搜索功能！对我来说，最大的难点之一是安装 CLI 并将其连接到 Dropbox 以实现跨平台共享。todo.txt 文档没有提供太多的一步一步地开始使用家酿 OSX，他们只是说去用家酿安装，然后你必须通过挖掘提示和技巧来找到不同的参数来设置定制你的体验。

本文将带您在 OSX 上用家酿软件设置 [todo.txt](http://todotxt.com) CLI，并将您的 todo.txt 文件存储在 Dropbox 中，以便在多个平台上使用。

抬头！确保将任何`<your username>`更改为您的 OSX 用户名

1.  使用以下命令安装 todo.txt CLI 和 homebrew

    `$ brew install todo-txt`

2.  在你的 Dropbox 文件夹中创建一个名为“todo”的文件夹

3.  在 Dropbox 文件夹的新文件夹中创建以下文件

    *   todo.txt
    *   所有. txt.bak 文件
    *   报告. txt
    *   done.txt

    或者从命令行，您可以进入您的新文件夹并运行这个命令

    `$ touch todo.txt todo.txt.bak report.txt done.txt`

4.  在新的 Dropbox 文件夹中创建 todo.cfg 的副本

    `$ cp /usr/local/Cellar/todo-txt/2.9/todo.cfg $HOME/Dropbox/todo`

5.  更新新的 todo.cfg 文件以反映 todo 目录的位置。这应该在第 4 行，将`/Users/gina/Documents/todo`改为`$HOME/Dropbox/todo`

6.  用这些行更新您的`~/.bash_profile`

    `source /usr/local/Cellar/todo-txt/2.9/etc/bash_completion.d/todo_completion
    complete -F _todo t`

    `alias t='/usr/local/Cellar/todo-txt/2.9/bin/todo.sh -d $HOME/Dropbox/todo/todo.cfg'`

    这将为您提供`t`的快捷方式，因此您不必每次都键入 todo.sh。它还会为`t`添加 tab complete。

7.  重新启动您的终端，现在您应该能够使用

    `$ t add “My first task”`

有关 todo.txt 的更多信息，请参见[http://todotxt.com](http://todotxt.com)