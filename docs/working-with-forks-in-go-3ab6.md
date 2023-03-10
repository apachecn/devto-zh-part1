# 在 Go 中使用叉子

> 原文：<https://dev.to/loderunner/working-with-forks-in-go-3ab6>

## 正确使用叉子

[![Dinglehopper me on GitHub](img/b13c85ff033d2f51140120fe7ec55e48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KC3HEkKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://toldbydesign.com/wp-content/uploads/2013/01/Told-By-Design-Walt-Disney-Little-Mermaid-Scuttle-dinglehopper-fork.jpg)

你曾经想要创建一个你正在使用的 Go 包的分支吗？也许你需要添加一个特性，特定于你的用例，除了在代码内部修改之外没有别的办法。或者，您可能想修复一个 bug，并向一个开源项目提交一份 PR。

无论是哪种情况，Go 及其基于位置的导入系统都使得交换包的实现变得很困难。如果您试图将它换到适当的位置，那么一旦您将数据类型传递给另一个文件中定义的函数，您很可能会得到类似于:`cannot use s (type "fork".SomeStruct) as type "original".SomeStruct in argument to SomeFunc`的错误。

您可以通过大量的重构来解决这个问题——在您自己的代码中更改导入，并对进出另一个包的所有内容进行类型转换——但是对于一个包含许多文件的大型项目来说，这几乎是不可能的。

类型别名会有所帮助，但是使用一点点 Git 魔法，您可以无缝地将一个包换成您的 fork，并继续破解。

## 分叉并修复

[![Not like that, no](img/ba75c9c1fce698eec62285027c7823da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T8KyX1QL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/zGskC7Y.jpg)

关于如何创建一个 fork 和提交一个 PR 我就不赘述了，GitHub 有[广泛的](https://guides.github.com/activities/forking/) [文档](https://help.github.com/articles/fork-a-repo/) [关于主题](https://help.github.com/articles/creating-a-pull-request-from-a-fork/)。我假设你已经点击了 GitHub 上的“Fork”按钮，现在你有了自己的包含原始源代码副本的存储库。

但是正如我们已经说过的，你不能仅仅`go get`分叉的库，因为所有的导入路径都是错误的。我们想要的是让您的本地分支，在您的`GOPATH`中，跟踪您分叉的远程存储库，而不是原始的。

让我们从添加遥控器开始。从程序包源路径中，键入以下内容:

```
$ git remote add forked <remote repository url> 
```

Enter fullscreen mode Exit fullscreen mode

然后，让我们确保本地分支跟踪`forked`遥控器，而不再跟踪`origin`。

```
$ git branch -u forked/master
Branch master set up to track remote branch master from forked. 
```

Enter fullscreen mode Exit fullscreen mode

在这一行之后，你可以开始提交和推送到你自己的 fork，所有依赖于这个 repo 的 Go 包都会透明地使用你自己的代码 fork。

这是很有用的，例如，如果你确定一个 bugfix，因为你可以开始使用代码的固定版本，而不需要等待变更被合并到上游。

当您想要恢复到跟踪原点时，可能是因为您的 PR 已经被合并，并且您已经删除了您的 fork，或者因为您需要将原始代码用于其他项目，只需键入:

```
$ git branch -u origin/master
Branch master set up to track remote branch master from origin.

# Reset local branch to remote HEAD
$ git reset --hard origin/master 
```

Enter fullscreen mode Exit fullscreen mode

* * *

有了这几行 git，我可以快速地派生并处理一个开源的 Go 包。这让我在开源项目上的合作变得更加容易。你在开源 Go 项目上合作的工作流程是怎样的？你看到任何错误或警告了吗？欢迎在下面评论！