# 在 git 中使用另一个私钥

> 原文：<https://dev.to/adamkdean/using-another-private-key-with-git-48hi>

不同的 git 服务器有不同的私钥是很正常的，但是如何像 SSH 一样提供不同的身份文件呢？

答案在一个文本文件中；`config`。

在您的 ssh 目录中创建一个文本文件，通常是`.ssh`:

```
$ touch ~/.ssh/config 
```

Enter fullscreen mode Exit fullscreen mode

然后用你最喜欢的文本编辑器打开它，对我来说，这是目前的 atom:

```
$ atom ~/.ssh/config 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以使用这个文件来配置不同的主机。下面的内容应该是不言自明的。供您参考，如果您有点困惑，身份文件是您的私钥。

```
Host example.com
    HostName git.example.com
    User git
    IdentityFile /Users/adam/.ssh/yourkey
    IdentitiesOnly yes 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到主机和主机名是不同的。这意味着您可以为 example.com 配置一个主机，该主机实际上指向另一个主机名，例如源控制服务器，例如 git.example.com。