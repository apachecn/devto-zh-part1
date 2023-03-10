# 使用 SSHFS 连接到 Purdue CS 文件

> 原文：<https://dev.to/jayhankins/connect-to-purdue-cs-files-using-sshfs-2nl7>

这里有一个关于我如何远程访问我的 Purdue CS 文件的快速教程，这样我仍然可以使用所有我喜欢的本地开发工具，比如 Sublime Text 和我的 shell 定制。

* * *

## 在 Mac OS X 上安装所需软件

我假设你已经在 Mac 上安装了 [Brew](http://brew.sh/) 和[家酿木桶](https://caskroom.github.io/)。

用桶安装 [osxfuse](https://osxfuse.github.io/) :

```
brew cask install osxfuse 
```

Enter fullscreen mode Exit fullscreen mode

用桶安装`sshfs` :

```
brew cask install sshfs 
```

Enter fullscreen mode Exit fullscreen mode

## 挂载你的远程目录

运行以下命令将目录挂载到本地机器:

```
sshfs <username>@data.cs.purdue.edu:/homes/<username>/ ./desired_mountpoint -o auto_cache,reconnect,defer_permissions,noappledouble 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
sshfs hankinsj@data.cs.purdue.edu:/homes/hankinsj/ ./purdue -o auto_cache,reconnect,defer_permissions,noappledouble 
```

Enter fullscreen mode Exit fullscreen mode

现在转到您安装的目录。您应该会看到目录中的远程文件，并且您可以与它们进行交互，就像它们是 Mac 上的本地文件一样。

## 卸载您的远程目录

运行`umount`命令卸载目录:

```
umount ./desired_mountpoint 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
umount ~/purdue 
```

Enter fullscreen mode Exit fullscreen mode

* * *

当然，这可以在任何支持 SSH 的系统上运行，但是在我还是一名 CS 学生的时候，连接到 Purdue 服务器在家里进行项目工作是非常方便的。我发现它有点不稳定，这取决于您的网络连接，并且它很少从暂停状态优雅地恢复装载。然而，这是一种非常方便的远程访问文件的方式，同时仍然可以在本地进行开发。

你是普渡计算机系的学生吗？对你有帮助吗？有什么没起作用吗？让我知道 [@jay_hankins](////twitter.com/jay_hankins) ！