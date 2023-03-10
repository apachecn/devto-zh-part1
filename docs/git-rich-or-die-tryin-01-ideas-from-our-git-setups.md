# Git rich or die trying 01:来自 Git 设置的想法

> 原文：<https://dev.to/brainlessdeveloper/git-rich-or-die-tryin-01-ideas-from-our-git-setups>

这篇文章是关于 Git 设置的系列文章的一部分。在本系列中，我们将关注我们最常用的 Git 别名，特别是那些我们感兴趣的别名(即，not `c = commit`),它们提高了我们使用 Git 的效率。

完整的汇编可以在找到[。](https://tech.store2be.com/never-forgit/)

# 重置基础

对于下面的别名，我们将在别名中定义 shell 函数，然后直接调用它们。

## rbb

```
rbb = "!f() { git rebase -i HEAD~$1; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

根据通过第一个参数传递的提交数量进行交互式重新调整。在一个至少有三次提交的项目中使用`git rbb 3`,将会在您选择的编辑器中显示如下内容:

[![interactive rebase alias](img/6dc248442203c9312770fa08818762e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--37TB5KZQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kne1sylg1kis0b9c2pyt.jpg)

## cfix

我们还可以在命令周围使用反斜线，用 shell 进行评估，并将结果作为 git 别名的一部分传递。在下面的别名中，我们将它与`rev-parse`一起使用:

```
cfix = "!f() { git commit --fixup `git rev-parse --short HEAD` && git rebase -i HEAD~2 -i --autosquash; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

当出现以下情况时，我们使用`cfix`。假设您提交了带有消息`Fix the terrible bug`的东西，却发现修复并没有按照您预期的方式运行。所以你做出改变，然后说:

`git add .`

现在您提交新的、真正的修复:

`git commit -m "Fix the terrible bug... again"`

将新的更改添加到最后一次提交中不是很好吗？嗯，你可以用`rebase`来做:

`git rebase -i HEAD~2`

这将使你的编辑器看起来像这一部分的第一个图像。然后您可以将`pick {COMMIT HASH} Fix the terrible bug... again`行更改为`fixup {COMMIT HASH} Fix the terrible bug... again`并保存文件，得到想要的结果。

您可以做完全相同的事情，但是使用`cfix`可以避免大量的命令。下面是添加更改并运行`git cfix`后的示例屏幕:

[![use latest add as a fixup for last commit](img/983179cb8637f7a6f342e1e2f34484a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LGD1fhN0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r3izk440e546qayeimta.jpg)

使用`git commit --amend`可以得到类似的结果，但是使用`cfix`你也可以`squash`，或者甚至使用提交，和/或`reword`前一个。然而，有些人不建议重写 Git 历史。你可能想仔细阅读这个话题，从古老的[开始，你不应该说谎:git rebase，amend，squash，和其他谎言](http://paul.stadig.name/2010/12/thou-shalt-not-lie-git-rebase-ammend.html)。

# 测井

定制`git log`有很多不同的选择。一些最常见的选项是使用像`--graph`或`--decorate`这样的标志来传递的，但是主要的区别是`--pretty`。下面是我们的一些截图来展示它们:

[![pretty git log alias](img/3b09637b858f513d3e3998d471bf7db6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xc4bXux---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mfmcdm8wrner4yasrjsu.jpg)T3】

```
--pretty=format:'%h %ad | %s%d [%an]' 
```

Enter fullscreen mode Exit fullscreen mode

[![pretty git log alias](img/e67d4596b8c3822d50727f274399978b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yIptypLa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gj1vbtzlb7alajxw1dri.jpg)T3】

```
--pretty=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(auto)%d%C(reset)' 
```

Enter fullscreen mode Exit fullscreen mode

关于如何构建自己的`--pretty`系列的大量文档可以在[这里](https://git-scm.com/docs/pretty-formats)找到。一个完成的`git log`别名的例子是:

```
lg = git log --graph --abbrev-commit --decorate --pretty=format:'%h %ad | %s%d [%an]' 
```

Enter fullscreen mode Exit fullscreen mode

# 拉取请求

如果你已经安装了`hub` CLI ( [和这里的如何做](https://tech.store2be.com/cli/linux/hub/2017/03/28/installing-the-hub-cli-on-linux/)，你可以添加下面的别名到你的工具箱:

```
pr = !hub pull-request 
```

Enter fullscreen mode Exit fullscreen mode

如果您想知道，别名开头的`!`告诉 git 命令必须由 shell 运行。

* * *

*大家好，我们是 [store2be](https://www.store2be.com) ，一家总部位于柏林的初创公司，为短期零售空间建立了一个支持 SaaS 的市场。如果你喜欢我们发布的内容，你可能想看看[store 2 be 技术页面](https://tech.store2be.com)或者关注我们的[媒体频道](https://medium.com/store2be-tech)。*