# Git 101 —步骤 4:合并分支、合并冲突和精选

> 原文：<https://dev.to/mrfrontend/git-101--step-4-merging-branches-merge-conflicts--cherry-pick-1522>

[![](img/4545363e16e8a86f535054b1533f08c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B7b2vyQr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZtTijeGkqwQKauvvZZuJlg.png)

今天我想向您展示合并分支、修复合并冲突以及如何使用 cherry-pick 来提交您的本地分支是多么容易！

让我们激情澎湃，奔向终点🔥

在视频中，我向你展示了我所知道的一切。在视频下面，你可以用你需要的所有命令来检查笔记。

[https://www.youtube.com/embed/BTIyMEOOPEQ](https://www.youtube.com/embed/BTIyMEOOPEQ)

#### 1 Git 将不同的分支拉入当前分支！

确保您的两个分支在远程上都是最新的！

```
git merge tryout 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以测试您的代码是否仍然工作！如果有效，就把它推到遥控器上！

#### 2 成功合并后，删除另一个分支

像我们在前面的视频中讨论的那样，使用下面的命令来删除分支。

```
git branch -d tryout 
```

Enter fullscreen mode Exit fullscreen mode

#### 3 如果得到合并冲突怎么办？

有时候会发生别人和你一样改了同一行的情况。当你拉遥控器时，你会得到一个合并冲突。

解决这个问题最简单的方法就是通过检查你的代码来手动解决。在您修复它之后，提交这个更改并将其推送到远程！

#### 4 如果你想从另一个分支得到一个特定的提交变更呢？

这就是我们所说的樱桃采摘。使用 cherry-pick，您可以选择您想要的提交，将其拖到您正在处理的本地分支中。

只需找到提交 id 和这个命令。

```
git cherry-pick commitId 
```

Enter fullscreen mode Exit fullscreen mode

#### 需要帮忙吗？

如果你和 Git 一起工作，但是被卡住了？请在评论中告诉我，或者在 twitter 上打电话给我，我会❤️帮你的！

你喜欢这个帖子吗？请大家分享一下🙏

*原载于* [*奥前端博客*](https://blog.mrfrontend.org/2017/12/git-101-step-4-merging-branches-merge-conflicts-cherry-pick/) *。*

* * *