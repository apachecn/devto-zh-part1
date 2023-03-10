# Git:挤压提交

> 原文：<https://dev.to/katpadi/git-squash-commits-48c6>

有了 git，在与他人分享之前，可以将之前的提交压缩成一个。

## 第一步

例如，您想压缩最后 3 次提交:

`git rebase -i HEAD~3`

另一方面，如果您只想简单地压缩所有未推送的提交:

`git rebase -i origin/master`

如果您有许多提交，并且想要从某个提交开始:

`git rebase -i`

上面的任何命令都会提示打开您的编辑器，如下所示:

```
pick a92f09 Added new feature X
pick ca9f90a Some other stuff I did
pick d18a6h1 More stuff I did 
```

这将显示在您的编辑器中:

```
pick a92f09 Added new feature X
squash ca9f90a Some other stuff I did
squash d18a6h1 More stuff I did 
```

注意:如果您的配置中没有定义编辑器，您将会遇到`Could not execute editor`。只要做`git config --global core.editor /usr/bin/vim`就可以继续了。

## 第二步

接下来，我们可以配置 git 如何处理提交。比方说，我想继续提交 a92f09。Git 将下面的两个提交压缩到第一个提交中，将留给我们一个包含所有提交的提交。为此，请将您的文件更改为:

```
pick a92f09 Added new feature X
squash ca9f90a Some other stuff I did
squash d18a6h1 More stuff I did 
```

保存并退出。

就是这样。如果您想完成“一天的工作”或者想拥有一个干净简洁的 Git 历史，那么 Git squash 特别有用。

## TL；速度三角形定位法(dead reckoning)

使用`git rebase -i origin/master`并将第二次和后续提交中的“pick”替换为“squash”。

[![git squash?](img/d4d7ce6e6b4d6b9b52c42231d307b374.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ia6nf0z7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blog.katpadi.ph/wp-content/uploads/2016/12/temp_quash.jpg%3Fresize%3D267%252C178)

帖子 [Git: Squash Commits](http://blog.katpadi.ph/git-squash/) 最先出现在 [KatPadi 的 Point](http://blog.katpadi.ph) 上。