# 维护不同的 Git 身份

> 原文：<https://dev.to/maxlmator/maintaining-different-git-identities>

当使用 Git 时，我喜欢为我的私人和工作内容设置单独的“身份”:工作中的提交应该使用我的工作电子邮件，而私人项目中的提交应该使用我的私人帐户。到目前为止，只要我注意到由错误的电子邮件执行的提交，我就会对每个存储库进行配置。

当我安装我的新电脑并编辑全局`.gitconfig`时，我想知道是否有更好的方法将它分开。原来有一个:随着 2.13 版的[发布，Git 引入了](https://github.com/blog/2360-git-2-13-has-been-released)[“条件包含”](https://git-scm.com/docs/git-config#_conditional_includes)。

有了这些 includes，我可以设置一个特定的 gitconfig-file 来包含特定位置的所有存储库。当我将我所有的工作项目存储在文件夹`~/Work`中时，我将默认的 user-config 设置为我的私有文件，并在该位置包含所有 Git 存储库的特定于工作的配置文件。

```
# ~/.gitconfig

[user]
    name = Firstname Lastname
    email = <private email address>

[includeIf "gitdir:~/Work/"]
    path = .gitconfig-work 
```

Enter fullscreen mode Exit fullscreen mode

```
# ~/.gitconfig-work

[user]
    name = Lastname, Firstname
    email = <work email address> 
```

Enter fullscreen mode Exit fullscreen mode

现在，被错误关联的提交所困扰的风险大大降低了——直到我开始将工作项目签出到~/Desktop/tmp 以进行小的修复。

要检查您的配置，请确保位于 Git 跟踪的目录中。非 Git 目录将总是显示默认配置:

```
$ cd ~/dev/justcurious 
$ git config user.email
<private email address>

$ cd ~/Work/projectA
$ git config user.email
<work email address>

$ cd ~/Work/not-a-repo
$ git config user.email
<private email address> 
```

Enter fullscreen mode Exit fullscreen mode