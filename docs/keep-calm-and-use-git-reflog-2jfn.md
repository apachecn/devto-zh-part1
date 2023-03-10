# 保持冷静，使用 Git Reflog

> 原文：<https://dev.to/rnowif/keep-calm-and-use-git-reflog-2jfn>

Git 是一个非常强大的工具，但是要掌握它需要相当长的时间。当您修改一些提交、解决冲突或强制推动您的更改时，破坏或丢失东西可能会特别容易。

在这篇博文中，我将向您展示如何使用`git reflog`命令来检索您认为已经永远丢失的提交。

## 检索修改后的提交

我将展示的第一个用例是当您修改提交时:

```
$ git init
$ echo "Hello" > hello.txt
$ git add hello.txt && git commit -m "Add hello"
$ echo "Hello!" > hello.txt
$ git add hello.txt && git commit --amend 
```

如果你认为第一次提交失败了，请继续阅读。

`git reflog`命令允许您显示在 repo 上执行的所有 git 命令，最重要的是，显示相关的提交散列。事实上，这些提交并没有丢失，它们只是不在树中了，但是您仍然可以检查它们，精选它们或者做您需要做的任何事情。

```
$ git reflog
3e6e190 HEAD@{0}: commit (amend): Add hello
51587bc HEAD@{1}: commit (initial): Add hello
$ git diff 51587bc 3e6e190
diff --git a/hello.txt b/hello.txt
index e965047..10ddd6d 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1 +1 @@
-Hello
+Hello! 
```

## 恢复错误的合并

第二种情况是，当您将一个分支基于另一个分支时，您会遇到冲突。

```
$ git checkout -b develop
$ git checkout master
$ echo "Hello, world!" > hello.txt
$ git add hello.txt && git commit -m "Hello, world!"
$ git checkout develop
$ echo "Hello, you!" > hello.txt
$ git add hello.txt && git commit -m "Hello, you!"
$ echo "World!" > world.txt
$ git add world.txt && git commit -m "Add world.txt" 
```

这是结果树(注意 hello.txt 在两个分支上被修改了，有冲突):

```
* a5c7ca5 - (HEAD -> develop) Hello, you!
| * 4d016c5 - (master) Hello, world!
|/  
* 3e6e190 - Add hello 
```

不出所料，`reflog`看起来像这样:

```
$ git reflog
21c2ce2 HEAD@{0}: commit: Add world.txt
a5c7ca5 HEAD@{1}: commit: Hello, you!
3e6e190 HEAD@{2}: checkout: moving from master to develop
4d016c5 HEAD@{3}: commit: Hello, world!
3e6e190 HEAD@{4}: checkout: moving from develop to master
3e6e190 HEAD@{5}: checkout: moving from master to develop
3e6e190 HEAD@{6}: commit (amend): Add hello
51587bc HEAD@{7}: commit (initial): Add hello 
```

现在，我们在`master`上重设`develop`的基础，并解决冲突。

```
$ git rebase master
// ... resolve merge conflicts 
```

事情是我们搞错了，拿错了`hello.txt`的版本。因为我们做了一个重新排序，我们重写了历史，我们可以认为我们失去了`hello.txt`的“好”版本。

[![](img/316de9bf24136e737304784f88ecaedf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I6wmvDRH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rnowif.github.img/git-reflog.png)

事实上，`reflog`将为您重新设置的每个提交显示一行，因此很容易找出您出错的地方。

```
$ git reflog
ae9d1d8 HEAD@{0}: rebase finished: returning to refs/heads/develop
ae9d1d8 HEAD@{1}: rebase: Add world.txt
4d016c5 HEAD@{2}: rebase: checkout master
21c2ce2 HEAD@{3}: commit: Add world.txt
a5c7ca5 HEAD@{4}: commit: Hello, you!
$ git diff 21c2ce2
diff --git c/hello.txt w/hello.txt
index 624d875..af5626b 100644
--- c/hello.txt
+++ w/hello.txt
@@ -1 +1 @@
-Hello, you!
+Hello, world! 
```

如果您想返回并修复它，您只需检查提交，删除当前的`develop`分支(因为它没有处于正确的状态),并基于正确的提交创建一个新的`develop`分支。

```
$ git checkout 21c2ce2
$ git branch -D develop
$ git checkout -b develop 
```

## 结论

我想你明白了，Git 会保留你提交的所有东西。因此，如果您发现自己处于删除以前提交的信息的情况下，不要惊慌，考虑使用`git reflog`命令深入到您的历史中，寻找您认为已经丢失的信息！