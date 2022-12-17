# Git 提示和技巧

> 原文：<https://dev.to/allanmacgregor/git-tips-and-tricks-59l0>

最近我收集了大量的 git 快捷方式、技巧和窍门。以下命令已经在 linux 上测试过，其中一些命令需要 git 的最新版本。

我希望你们中的许多人会像我一样发现它们很有用。因此，让我们从一些简单但有用的命令开始，来搜索和查看您的 git 存储库历史。

## 只显示特定分支的日志

如果您正在处理一个具有特定不良历史或分支日志
的项目，可以使用以下命令

```
git log --first-parent {branch_name} 
```

Enter fullscreen mode Exit fullscreen mode

## 将日志显示为单行

以下命令将显示提交散列和提交注释的第一行。

```
git log --pretty=oneline --abbrev-commit 
```

Enter fullscreen mode Exit fullscreen mode

上面命令的一个更高级、更好的版本是:

```
git log --graph --pretty=oneline --abbrev-commit --date=relative 
```

Enter fullscreen mode Exit fullscreen mode

## 在整个 git 历史的所有提交中搜索一个字符串

当寻找一段特定的代码以查看它何时被添加/删除时，这真的很有用:

```
git log -S{text_to_search} 
```

Enter fullscreen mode Exit fullscreen mode

## 显示所有合并到当前分支的分支

```
git branch --merged 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们可以看到所有尚未合并的分支。

```
git branch --no-merged 
```

Enter fullscreen mode Exit fullscreen mode