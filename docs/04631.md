# 以编程方式获取当前 git 分支

> 原文：<https://dev.to/math2001/programmatically-get-current-git-branch>

很好很容易。适用于 git 1.6.3 和更高版本。

```
$ git rev-parse --abbrev-ref HEAD
master 
```

Enter fullscreen mode Exit fullscreen mode