# 不要修正，修正

> 原文：<https://dev.to/tmr232/dont-amend-fix>

作为 git 用户，我们知道我们应该“尽早提交，经常提交”虽然这是一件很棒的事情，但它确实意味着我们有时会犯错误，需要修复提交。也许我们忘记了`git add`一个新文件，或者漏掉了一个打印错误。所以我们继续前进`git commit --amend`。问题解决了。太好了。

但是我个人很讨厌。

首先，修改提交隐藏了历史。一旦你修改了，在你能正确地测试新的状态之前，过去的状态就消失了。的确，你也可以通过`git reflog`来恢复它，但是没有人真的喜欢使用它。应该是万不得已。

另一方面，修改是非常有限的。假设我正在编写一些 C 代码。我写了我的第一个模块，添加它并提交。

```
git add FirstModule.h
git commit -m "Added FirstModule" 
```

Enter fullscreen mode Exit fullscreen mode

我写了第二个模块，并添加了它。

```
git add SecondModule.h SecondModule.c
git commit -m "Added SecondModule" 
```

Enter fullscreen mode Exit fullscreen mode

现在，在添加了第二个提交之后，我意识到我忘记提交`FirstModule.c`。`git commit --amend`来救援？不完全是。我现在不得不求助于被称为`git rebase`的黑色可怕的巫毒魔法。

首先，我们提交被遗忘的模块

```
git add FirstModule.c
git commit -m "Added FirstModule.c, forgotten eariler." 
```

Enter fullscreen mode Exit fullscreen mode

然后再还原-`git rebase -i HEAD~3`-

```
pick 1db8687 Added FirstModule
pick 336941b Added SecondModule
pick 7884909 Added FirstModule.c, forgotten eariler. 
```

Enter fullscreen mode Exit fullscreen mode

更改为

```
pick 1db8687 Added FirstModule
fixup 7884909 Added FirstModule.c, forgotten eariler.
pick 336941b Added SecondModule 
```

Enter fullscreen mode Exit fullscreen mode

保存并退出，我们就完成了。

```
* 1946e37d105ffebcbd91bb958f8a2fce6160c761 (HEAD -> master) Added SecondModule
|  create mode 100644 SecondModule.c
|  create mode 100644 SecondModule.h
* 8ffbb9f2915e060a6c4771e13f5a82442743724c Added FirstModule
|  create mode 100644 FirstModule.c
|  create mode 100644 FirstModule.h
* 815e7bab6ee1fa5bf1df10f5705919b48cbe214c First Commit 
```

Enter fullscreen mode Exit fullscreen mode

没那么难吧。

但是，在修改和重定基准之间转换可能会很麻烦。尤其是大多数时候，没有真正的必要重新确定基础，并且很容易忘记这个过程。输入`git commit --fixup`(或`--squash`)和`git rebase -i --autosquash`。

这些命令为我们节省了重新排序提交和从`pick`更改为`fixup`或`squash`的工作。让我们的重置工作变得容易多了。

我喜欢定义以下别名:

```
[alias]
    ri = rebase -i --autosquash
    mri = rebase -i
    fix = commit --fixup
    squ = commit --squash 
```

Enter fullscreen mode Exit fullscreen mode

使用这些别名，我们之前做的重定基础工作如下:

```
git add FirstModule.c
git fix HEAD~1
git ri HEAD~3 
```

Enter fullscreen mode Exit fullscreen mode

我们会自动得到下面的 rebase

```
pick 1db8687 Added FirstModule
fixup 50a3650 fixup! Added FirstModule
pick 336941b Added SecondModule 
```

Enter fullscreen mode Exit fullscreen mode

退出编辑器，完成它。

在重设基础之前，我们可以任意多次使用`fix`(直接使用`git fix HEAD -a`)。我们的日志可能看起来很有趣

```
* fe0c2a0 (HEAD -> master) fixup! fixup! fixup! fixup! Added SecondModule
* a53cd32 fixup! fixup! fixup! Added SecondModule
* 9c19f2d fixup! fixup! Added SecondModule
* b758a53 fixup! Added SecondModule
* 902d65e Added SecondModule
* 67f1260 Added FirstModule
* 815e7ba First Commit 
```

Enter fullscreen mode Exit fullscreen mode

但是 rebase 不在乎

```
pick 902d65e Added SecondModule
fixup b758a53 fixup! Added SecondModule
fixup 9c19f2d fixup! fixup! Added SecondModule
fixup a53cd32 fixup! fixup! fixup! Added SecondModule
fixup fe0c2a0 fixup! fixup! fixup! fixup! Added SecondModule 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

停止使用`git commit --amend`，开始使用`git fix` ( `git commit --fixup`)代替。这是一个无所畏惧、低开销的选择，而且更加灵活。
如果你需要的话，以下是化名:

```
[alias]
    ri = rebase -i --autosquash
    mri = rebase -i
    fix = commit --fixup
    squ = commit --squash 
```

Enter fullscreen mode Exit fullscreen mode