# 使用 ssh 别名的 git 远程

> 原文：<https://dev.to/bharris/git-remotes-with-ssh-aliases--3jno>

你知道别名适用于 git 遥控器吗？？

~/.ssh/config

```
Host gh 
HostName github.com 
User git 
IdentityFile ~/.ssh/gh\_key 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以用`gh:username/repo`代替`git@github.com:username/repo`作为遥控器，后者要短得多，也更容易打很多次！

`git clone gh:benharri/learngit`

`ssh_config`文件还有许多其他的用例。例如，下面是我对 tilde 机器的配置，以便于 ssh 连接。

```
Host tilde 
HostName tilde.team 
User ben 
```

Enter fullscreen mode Exit fullscreen mode

然后使用`ssh tilde`开始一个新的 ssh 会话。这也适用于`scp`:尝试用`scp file.txt tilde:workspace/.`代替`scp file.txt ben@tilde.team:workspace/.`

ssh_config 文件非常有用。查看`man ssh_config`获得选项的完整列表！

标签: [git](//tag_git.html) ， [ssh](//tag_ssh.html)

[评论？推特](http://twitter.com/intent/tweet?url=https://tilde.team/~ben/blog/git-remotes-with-ssh-aliases.html&text=<Type%20your%20comment%20here%20but%20please%20leave%20the%20URL%20so%20that%20other%20people%20can%20follow%20the%20comments>&via=nebsirrah)