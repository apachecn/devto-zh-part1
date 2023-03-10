# 使用 Git 补丁添加特定行

> 原文：<https://dev.to/satansdeer/add-specific-lines-with-git-patch-685>

*本帖最初发表于[maksimivanov.com](http://maksimivanov.com/posts/git-add-patch)T3】*

*这个帖子有视频版本，请务必在[YouTube](https://www.youtube.com/watch?v=b0t8XL6ZoeQ)T3 上查看*

您知道`git add`将文件添加到索引中。但是你知道它可以添加**特定行**的文件吗？或者甚至添加文件，忽略其内容？我们来看看这个！

首先让我们熟悉一下`git add --patch`命令或者它的简写`git add -p`。它是做什么的？

让我们想象你一直在做某项工作。在这个过程中，你被冲昏了头脑，引入了一些与当前任务无关的变化。

现在是提交更改的时候了，但是有些是不相关的。把一切都放在承诺中是错误。

这就是`git add --patch`发挥作用的地方。

## 见例题

想象一下，我们有一个文件 *poem.txt* ，里面有两句未完成的对联:

```
Roses are red
Violets are blue

Roses are red
Violets are blue 
```

Enter fullscreen mode Exit fullscreen mode

经过一整天的辛勤工作，我们完成了两项任务:

```
➜  git_add_patch (master) ✗ git diff

diff --git a/poem.txt b/poem.txt
index 39f13e6..f4b70ab 100644 -------- a/poem.txt +++ b/poem.txt @@ -1,5 +1,9 @@
 Roses are red
 Violets are blue
+Sugar is sweet
+And so are you 
 Roses are red
 Violets are blue
+Onions stink
+And so do you. 
```

Enter fullscreen mode Exit fullscreen mode

但现在我们有点担心第二个对联，只想提交第一部分。

```
➜  git_add_patch (master) ✗ git add -p

diff --git a/poem.txt b/poem.txt
index 39f13e6..f4b70ab 100644 -------- a/poem.txt +++ b/poem.txt @@ -1,5 +1,9 @@
 Roses are red
 Violets are blue
+Sugar is sweet
+And so are you 
 Roses are red
 Violets are blue
+Onions stink.
+And so do you. Stage this hunk [y,n,q,a,d,/,s,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

看最后一行， **Stage 这个 hunk [y，n，q，a，d，/，s，e，？】**？在这里，它为您提供了一些选项，其中最重要的是:

*   **y** -上演这出大戏
*   不要上演这个大块头
*   **q**——退出；不要上演这个大块头或任何剩余的
*   **a** -在文件中存放这个块和所有后面的块
*   **d** -不要存放这个块或文件中任何后面的块
*   将当前块分割成更小的块
*   **e** -手动编辑当前块

还有一些选项，你可以通过选择**来查看？**选项。

这里我们只需要关于糖和甜度的第一部分，所以我们选择选项 **s** 。

```
Stage this hunk [y,n,q,a,d,/,s,e,?]? s
Split into 2 hunks.
@@ -1,5 +1,7 @@
 Roses are red
 Violets are blue
+Sugar is sweet
+And so are you 
 Roses are red
 Violets are blue
Stage this hunk [y,n,q,a,d,/,j,J,g,e,?]? y 
```

Enter fullscreen mode Exit fullscreen mode

我们选择上演那个大块头，现在它问我们是否要上演第二个:

```
Stage this hunk [y,n,q,a,d,/,j,J,g,e,?]? y
@@ -3,3 +5,5 @@

 Roses are red
 Violets are blue
+Onions stink
+And so do you. Stage this hunk [y,n,q,a,d,/,K,g,e,?]? n 
```

Enter fullscreen mode Exit fullscreen mode

而我们又不想上演这一出，所以选择 **n** 。

太好了，让我们看看现在有什么。

```
➜  git_add_patch (master) ✗ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    modified:   poem.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   poem.txt 
```

Enter fullscreen mode Exit fullscreen mode

酷豆，是时候做这件事了。

```
➜  git_add_patch (master) ✗ git commit -m "Complete the first couplet" 
```

Enter fullscreen mode Exit fullscreen mode

Git 通过空行分割大块，但是如果你真的只需要存放特定的行怎么办？

## 目睹 E 的力量

想象你有一个`diff`像这样:

```
➜  git_add_patch (master) ✗ git diff

diff --git a/poem.txt b/poem.txt
index 9737e9f..a2baecd 100644 -------- a/poem.txt +++ b/poem.txt @@ -5,3 +5,6 @@ And so are you

 Roses are red
 Violets are blue
+Flowers smell good
+Onions stink
+And so do you. 
```

Enter fullscreen mode Exit fullscreen mode

你真的很关心洋葱的部分，但你不想把它去掉。所以你只想上演*【花香】**【你也是】*的台词。

现在 **s** 选项对我们没有帮助，因为这些行没有被空行分开，git 认为它们是一个整体。

**e** 选择救援。

```
➜  git_add_patch (master) ✗ git add -p
diff --git a/poem.txt b/poem.txt
index 9737e9f..a2baecd 100644
-------- a/poem.txt
+++ b/poem.txt
@@ -5,3 +5,6 @@ And so are you

 Roses are red
 Violets are blue
+Flowers smell good.
+Onions stink.
+And so do you.
Stage this hunk [y,n,q,a,d,/,e,?]? e 
```

Enter fullscreen mode Exit fullscreen mode

它将打开默认的文本编辑器，您可以手动编辑它。

[![editor](img/949365daefb4362587543b16a1f2d7a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dBaiJe9h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://d33wubrfki0l68.cloudfront.net/5d88c10dd411885ef8335247749f9f90dcac5515/9212e/asseimg/edit_hunk.png)

去掉洋葱的部分就可以了:

```
➜  git_add_patch (master) ✗ git add --staged

diff --git a/poem.txt b/poem.txt
index 9737e9f..a2baecd 100644 -------- a/poem.txt +++ b/poem.txt @@ -5,3 +5,6 @@ And so are you

 Roses are red
 Violets are blue
+Flowers smell good
+And so do you. 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在你可以决定以后如何处理这些臭洋葱了。

但是如果您需要从新文件中添加一些特定的行呢？

## 使用意图添加

使用`git add --intent-to-add`或`git add -N`添加特定文件，但不添加其内容。

想象一下，我们从头开始写诗。

现在`git diff`什么都不显示，如果我们使用`git add -p`，它会显示`No changes.`

没问题，让我们告诉 git 文件存在。

```
➜  git_add_patch (master) ✗ git add -N poem.txt 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`git add -p`然后编辑大块来删除关于洋葱的线条。