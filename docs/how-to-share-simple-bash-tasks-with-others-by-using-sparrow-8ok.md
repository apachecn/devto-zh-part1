# 如何使用 Sparrow 与他人共享简单的 Bash 任务

> 原文：<https://dev.to/melezhik/how-to-share-simple-bash-tasks-with-others-by-using-sparrow-8ok>

Sparrow 是一个 Linux 脚本平台，可以简化日常的脚本工作和发布。在这篇小文章中，我将向你展示如何与他人分享简单的 Bash 任务。

# 安装麻雀

```
$ sudo yum install curl
$ cpanm -q --notest Sparrow 
```

Enter fullscreen mode Exit fullscreen mode

# 创建 Bash 任务

首先我们需要 Bash 插件:

```
$ sparrow index update
$ sparrow plg install bash 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们创建一个任务。我总是忘记如何安装 Rvm/Ruby，所以我需要一个助记命令:

```
$ sparrow project create utils
$ sparrow task add utils ruby-rvm-stable bash 
```

Enter fullscreen mode Exit fullscreen mode

```
$ EDITOR=nano && sparrow task ini utils/ruby-rvm-stable
command: "\curl -sSL https://get.rvm.io | bash -s stable --ruby" 
```

Enter fullscreen mode Exit fullscreen mode

# 分享 Bash 任务

首先，你需要在 [SparrowHub](http://sparrowhub.org/) 获得一个账户，因为你要将你的任务保存到这个账户中。

然后设置好凭证:

```
$ cat ~/sparrowhub.json

{
  "user"  : "melezhik",
  "token" : "*****-****-****-*******-*****"
} 
```

Enter fullscreen mode Exit fullscreen mode

只需上传任务和一些有用的描述:

```
$ sparrow remote task upload utils/ruby-rvm-stable "Install RVM stable with ruby" 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，您的任务是私人的，因此只有您可以看到它们。让我们采取简单的步骤来改变这一点:

```
$ sparrow remote task share utils/ruby-rvm-stable 
```

Enter fullscreen mode Exit fullscreen mode

# 运行远程 Bash 任务

现在团队的其他成员可以享受你的小任务了。为此，他们只需要安装 Sparrow 客户端:

```
$ sparrow remote task run melezhik@utils/ruby-rvm-stable 
```

Enter fullscreen mode Exit fullscreen mode

# 社区远程任务

要列出 SparrowHub 社区远程任务，只需这样说:

```
$ sparrow remote task public list 
```

Enter fullscreen mode Exit fullscreen mode

进一步阅读是关于远程任务的 Sparrow [文档](https://github.com/melezhik/sparrow#remote-tasks)。

# 结论

Sparrow 能够以简单有趣的方式创建脚本。或者如果你想——“快速和肮脏的方式来开发一些有用的脚本，并与他人分享”。