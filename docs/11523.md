# 使用 Git 对提交进行计数

> 原文：<https://dev.to/adamkdean/counting-commits-with-git-5gli>

要对分支上的提交进行计数，可以使用`git rev-list`。

对于您所在的分支，请使用:

```
git rev-list --count HEAD
5 
```

Enter fullscreen mode Exit fullscreen mode

或者对于另一个`<branch>`，使用:

```
git rev-list --count <branch>
5 
```

Enter fullscreen mode Exit fullscreen mode

此外，要按作者统计提交次数，可以使用`git shortlog`:

```
git shortlog -s -n
4 Some User
1 Joe Bloggs 
```

Enter fullscreen mode Exit fullscreen mode