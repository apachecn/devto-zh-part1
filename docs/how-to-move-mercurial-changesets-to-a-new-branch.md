# 如何将 Mercurial 变更集移动到新的分支

> 原文：<https://dev.to/monknomo/how-to-move-mercurial-changesets-to-a-new-branch>

这就是我，当我意识到我一整天都在犯错误的时候:

[通过 GIPHY](http://giphy.com/gifs/foxtv-office-l41lSR9xZubfd2Qve)

幸运的是，有一种方法可以在分支之间移动变更集，前提是您没有将您的变更发布到任何其他存储库中。如果您已经将您的更改推送到母舰存储库，或者您的同事，收拾残局的唯一方法是将回购的所有用户召集在一起并进行协调。这超出了本文的范围，对于除了最小的或独裁的团队之外的所有人来说太难了。

# 问题设置

在 Reci-p.com，我们制作食谱，销售广告。我一直在努力写新食谱。以下是我的开场白:

```
$ hg history
changeset:  1:b1c330d2ac79
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Feb 04 05:15:00 2017 -0900
summary:    Added quesadilla recipe

changeset:  0:6f5ef8f77a9f
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Jan 28 11:30:00 2017 -0900
summary:    Added taco recipe 
```

我想添加饮料配方，但它们还没有准备好向我们有价值的用户展示，所以我想在一个开发分支中添加它们。不幸的是，我整晚都在外面品尝饮料配方，却忘了创建新的分店！

[通过 GIPHY](http://giphy.com/gifs/e3FaCx7T425qg)

这就是我的结局:

```
$ hg history    
changeset:  3:398dfc004270
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Feb 04 05:15:00 2017 -0900
summary:    Added michela recipe

changeset:  2:852364587134
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Feb 04 05:15:00 2017 -0900
summary:    Added mojito recipe

changeset:  1:b1c330d2ac79
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Feb 04 05:15:00 2017 -0900
summary:    Added quesadilla recipe

changeset:  0:6f5ef8f77a9f
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Jan 28 11:30:00 2017 -0900
summary:    Added taco recipe 
```

幸运的是，在我将所有代码推送到母舰回购之前，我注意到我一直在错误的分支中工作。

# 将我的代码移动到不同的分支

解决这个问题的方法是创建丢失的分支，然后从我的新变更集中创建一个补丁，将补丁应用到新的分支，然后从不正确的分支中剥离变更集。

在与上面相同的存储库中工作:

```
$ hg export -o patch.diff 3 2 
```

我创建了一个补丁，它显示为一个名为`patch.diff`的文件。

在我开始添加新内容之前，从最新的变更集中创建一个分支:

```
$ hg update 1
$ hg branch drinks
$ hg commit 
```

修补我刚做的新分支:

```
$ hg import patch.diff 
```

这是危险的，所以请确保您的目标是正确的变更集。你不能轻易撤销这个。您还需要启用[条延伸](https://www.mercurial-scm.org/wiki/StripExtension)

删除原始分支的更改，这样我们可以避免混合饮料和食物配方:

```
$ hg strip 3 
```

唷，我的仓库修好了。现在，当查看历史记录时，我可以看到所有内容都在正确的分支中:

```
$ hg history -l 4
changeset:  4:8afca56c4940
branch:     drinks
tag:        tip
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Feb 04 05:15:00 2017 -0900
summary:    Added michela recipe

changeset:  3:357ff9545db
branch:     drinks
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Feb 04 05:15:00 2017 -0900
summary:    Added mojito recipe

changeset:  2:357ff9545db
branch:     drinks
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Feb 04 05:15:00 2017 -0900
summary:    drinks branch

changeset:  1:b1c330d2ac79
user:       Gunnar Gissel <gunnar@reci-p.com>
date:       Sat Feb 04 05:15:00 2017 -0900
summary:    Added quesadilla recipe 
```

现在我可以安全地推了，我不会不小心用混合饮料配方污染我们的配方

[通过 GIPHY](http://giphy.com/gifs/party-hard-adventure-time-J4ufHCZTcroys)

如果你喜欢这个，请访问我的博客。

感谢迈克尔·巴德的标题照片

[每月收到一封电子邮件，其中包含来自网络的优秀技术和技术领导文章](http://www.gunnargissel.com/pages/email-signup-1.html)