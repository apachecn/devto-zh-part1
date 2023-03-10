# 在 git 中轻松获取当前分支名称

> 原文：<https://dev.to/kakty3/easy-get-current-git-branch>

有时候我会想念 Git 没有的 [Mercurial](https://www.mercurial-scm.org/) 的特性。其中一个是 [hg outgoing](https://www.mercurial-scm.org/repo/hg/help/outgoing) ，它“显示在指定的目标存储库中或者默认的推送位置中没有找到的变更集”。使用`git log branchA..branchB`可以实现类似的行为，例如，查看*输出*提交:`git log origin/master..feature-branch`。

有一天，我对名字很长的分支使用了这个命令。我只需输入几个首字母，按下*键*，分支名称就自动完成了(我个人更喜欢**鱼**壳，但它应该也适用于所有其他现代壳)。我问自己:“如果我正在使用 **bash** 并且没有自动补全功能，但是我也不想使用`git branch`并复制粘贴分支名称，该怎么办？”。

答案是用`git rev-parse --abbrev-ref HEAD` :

```
> git rev-parse --abbrev-ref HEAD
feature-branch 
```

Enter fullscreen mode Exit fullscreen mode

我已经为它创建了别名:

```
git config --global alias.cb 'rev-parse --abbrev-ref HEAD' 
```

Enter fullscreen mode Exit fullscreen mode

其中 *cb* 代表*当前分支*。

现在我可以做以下事情

```
git log origin/master..$(git cb) 
```

Enter fullscreen mode Exit fullscreen mode

突然我发现不需要通过*远程分支*，之前的命令可以简化:

```
git log origin/master.. 
```

Enter fullscreen mode Exit fullscreen mode