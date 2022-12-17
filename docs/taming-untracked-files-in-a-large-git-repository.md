# 驯服大型 git 存储库中未跟踪的文件

> 原文：<https://dev.to/anotherdevblog/taming-untracked-files-in-a-large-git-repository>

你有没有试过在 Visual Studio 项目中添加一个 NuGet 包，却不小心把它添加到了整个解决方案中？现在你有 100 个未追踪的。配置文件显示在您的更改文件列表中？或者，您是否曾经在一个大的 rebase 中途停止 git，结果在您的机器上有许多新文件，您需要倒带并回到原始状态？

嗯，这就是为什么我们有

`git clean -fd`

。但是如果你像我一样在[一个非常大的代码库](http://blogs.atlassian.com/2014/05/handle-big-repositories-git/)中工作，那么这个命令将永远*运行。我说的是“去参加一个小时的会议，希望你回来的时候会议已经结束了”。*

幸运的是，如果显示为未被跟踪的文件数量是可控的，那么有一种比运行

`git clean`

:
更快的方法来清除那些讨厌的未被跟踪的文件

```
git ls-files --others --exclude-standard | xargs -n 1 rm -fr 
```

Enter fullscreen mode Exit fullscreen mode

我不是那种不加解释就发布命令或代码片段的人，但希望这足够明显。

`git ls-files --others --exclude-standard`

是将未跟踪的文件路径列表打印到 stdout 的神奇咒语(就像运行

`git status`

时看到的最后一段中打印的一样)。然后，我们将该列表通过管道传递给 xargs 实用程序，该实用程序一次遍历一个列表，并将每个文件路径传递给 delete (

```
 ) command, leaving you with no files left in the working directory that git doesn't know about.

I maintain a bash script that I use to auto-create and synchronize my git aliases across my computers [here](https://gist.github.com/watkinsmatthewp/79093d4bf0d55c2e7269f534f1b6a689). I've added these commands as

```git list-untracked-files```

 and

```git delete-untracked-files```

since I use them so often.

Know of any better ways to clean untracked files from a large codebase in Git? Leave a comment below! 


Enter fullscreen mode Exit fullscreen mode