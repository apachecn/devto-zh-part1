# 版本控制中的樱桃采摘

> 原文：<https://dev.to/restoreddev/cherry-picking-in-version-control-93>

最近，我一直在努力提高我的 git 知识。我认为这是一个在我们作为开发人员的日常工作中经常被忽视的工具，因为我们太专注于编写代码了。当版本控制出现问题时，很容易想要吹走一个分支，并手动将代码复制到一个新的分支，甚至删除本地存储库并再次克隆。让我们试着纠正与 git 已经提供的特性的冲突或不一致，而不是走这条艰难的道路。二话没说，跟*樱桃精*打个招呼。

首先，让我们打开一个新的终端窗口并创建一个名为`demo`的新项目文件夹。我将使用 Bash 命令，所以如果您使用 Windows，您将只使用 CMD 替代命令。

```
$ mkdir demo
$ cd demo 
```

最近，我一直在学习 Swift 创建一个 iPhone 应用程序，所以让我们在`demo`目录中创建一个名为`main.swift`的新 Swift 文件，并初始化一个新的 git 存储库。

```
$ touch main.swift
$ git init
$ git add -A
$ git commit -m "First commit" 
```

现在为了说明`cherry-pick`是如何工作的，我将创建另一个名为`feature`的分支。

```
$ git checkout -b “feature” 
```

在这个新的分支下，我们将创建一个名为`Foo.swift`的新文件并提交它。

```
$ touch Foo.swift
$ git add -A
$ git commit -m “Added Foo file” 
```

然后，我们将创建另一个名为`Bar.swift`的文件并提交它。

```
$ touch Bar.swift
$ git add -A
$ git commit -m “Added Bar file” 
```

所以现在，我们应该在我们的`feature`分支中有三个提交。让我们使用 git log 来查看它们。

```
$ git --no-pager log —-oneline
4f070d4 (HEAD -> feature) Added Bar file
7f96854 Added Foo file
865cc49 (master) First commit 
```

现在，让我们切换回我们的`master`分支。

```
$ git checkout master 
```

为了便于说明，让我们假设我们项目的需求已经改变，在我们的`master`代码中，我们只需要`Foo`文件而不是`Bar`文件。如果我们用我们的`feature`分支运行`git merge`，我们将得到两个文件。那么解决办法是什么呢？我们将挑选出我们想要的提交，这样我们就可以只将`Foo`文件添加到`master`中。从前面的`git log`中获取`Foo`文件的提交散列，我们将在下一个命令中使用它，并运行`git log`来查看结果。

```
$ git cherry-pick 7f96854
$ git --no-pager log --oneline
4e8af63 (HEAD -> master) Added Foo file
865cc49 First commit 
```

现在我们有了`master`中的`Foo`文件，而没有了`Bar`文件！

这个例子是一个非常简单的`cherry-pick`视图。你不希望在大型或复杂的合并中使用 Cherry picking，但是它是处理小的变化和冲突的一个很好的方法。如果您想更深入地了解，请查看`git rebase`了解更多高级分支变更。

快乐承诺！