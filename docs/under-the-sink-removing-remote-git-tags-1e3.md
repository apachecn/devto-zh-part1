# 在接收器下:移除远程 git 标签

> 原文：<https://dev.to/pvafc/under-the-sink-removing-remote-git-tags-1e3>

TLDR；git push[远程名称]:refs/tags/[标记名称]

干得好——继续。但是等等，如果你和我一样，你可能会想:“等等，这到底是怎么回事？”所以继续想象一个水管工的屁股裂缝——因为我们要在饭桶下偷看一眼。

<figure>[![](img/09b6d6c3019de841339bae5ee9992640.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QFvUeNJl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/330/1%2A0o9GZUzXiNnI4poEvxvy8g.png)

<figcaption>[*xkcd*](https://xkcd.com/1597/)</figcaption>

T10】</figure>

也许不是流行的观点，但是我最喜欢 git 的一部分是它的命令。我认为它们非常棒，这是我使用终端的主要原因之一。然而，我并没有偏离常见的 git 命令太远，对我来说，这些命令如下:

git[push]|[pull]|[check out]|[rebase]|[diff]|[log]

都(相对)容易理解和使用。这些被称为[瓷器指令](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)——它们隐藏了管道。管道是隐藏起来的所有部件，它们使 git 工作并在幕后做我们认为理所当然的事情——有点像你家里的管道。

因此，当我需要删除一个已经推到遥控器上的标签时，这个任务我过去已经做过好几次了，我想说的是，我只是想起来该怎么做，或者自己想出来了。我没有。我谷歌了一下，事实上，我甚至记得之前谷歌过几次。我没有理解这个命令，可能是因为我从来没有花时间去弄清楚*为什么*它会工作。所以，让我们开始吧。

> git push[远程名称]:refs/tags/[标记名称]

看起来很复杂；让我们分解一下(LC；lbtd)。

*   饭桶
*   推
*   [远程名称]例如*原点*

行..到目前为止一切顺利…

*   :refs/tags/[tag-name]例如:refs/tags/1.10.1

什么-------------------------------- f % * # K？好的，我想我已经确定了我需要关注的地方。

> :参考/标签/[标签名称]

让我们进一步分析一下。

*   ：
*   参考/标签/[标签名称]

稍微熟悉。git 文件夹，它在每个回购中，我可以告诉第二部分看起来像 the.git/refs 文件夹的路径。我还知道 refs 文件夹是存储所有 *git 引用*的地方。在查看了 push (git help push)的手册页，重点关注了 push 接受的选项之后，我发现冒号是 *refspec* 格式的一部分。这意味着整个器件:refs/tag/[tag-name]是一个 *refspec* 。在我们讨论 *refspecs* 之前，让我们回顾一下什么是 *git 引用*。

#### Git 引用

如前所述，git 引用存储在/。git/refs 文件夹。这里您会发现几个子文件夹，git 用它们来对引用进行分组。但是它们是什么呢？

```
$_: ls ./.git/refs/
heads remotes tags 
```

Enter fullscreen mode Exit fullscreen mode

git 中的引用，或文档中通常提到的“refs ”,指的是原始的 SHA-1 值。分支名称，**标签**，它们都是完整的 40 字符 SHA-1 校验和(+头)散列的友好别名。我不会详细介绍什么是内容可寻址文件系统或者它是如何工作的，但是要知道，git 就是其中之一，这些散列就是 git 如何能够*引用其中的*我们的数据。可以把它想象成一个键值存储，ref 就是键。

Git 在我们使用它的时候会一直为我们创建和更新这些别名。想象一下，当您想要签出或提取一个分支时，必须键入整个 40 个字符的散列？如果你找出一个裁判，你会看到这个:

```
$_: cat ./.git/refs/heads/master 
8d519a1066b4d05e010e0a6435401f0ea6fdca71 
```

Enter fullscreen mode Exit fullscreen mode

这些文件夹也存在于远程服务器上。事实上，如果你想备份你的 git repo，比如真正的 lo-fi，复制. git 文件夹。

#### ref spec

一个 *refspec* 是一个指定来源和目的地的格式，等等吧，*引用！* Git 自动为您添加的每个遥控器设置 refspec。如果您对 refspecs 的设置感到好奇，可以看看这个文件。/.您的 repo 中的 git/config。

```
$_: cat ./.git/config
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[remote "origin"]
        url = git@github.com:vprasanth/dotfiles.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master 
```

Enter fullscreen mode Exit fullscreen mode

> 格式:+ <src>:</src>

重要的是要明白，根据您是推还是拉，源和目的地会在本地和远程之间切换。当你推的时候，src 是*本地*，目的地是*远程*，如果你拉，反之亦然。

另一个需要知道的重要事情是，git 将引用扩展到它的完全限定名，它通过使用在配置中为远程设置的目的地来做到这一点(参见上面代码片段中 branch remote 如何设置为“origin”)。事实上 git 总是进行这种扩展。有没有尝试过推送遥控器上不存在的本地分支？:)

```
$_: git log -n 1 --oneline origin/master 
8d519a1 Remove vundle configs and set tab to 2
$_: git log -n 1 --oneline remotes/origin/master
8d519a1 Remove vundle configs and set tab to 2
$_: git log -n 1 --oneline refs/remotes/origin/master
8d519a1 Remove vundle configs and set tab to 2 
```

Enter fullscreen mode Exit fullscreen mode

是啊。

哦，关于 refspecs 还有一件事，按一个空的<src>告诉 git 将遥控器上的<dst>设置为空，然后 ***删除*** 它。</dst></src>

#### So… git 推送原点:refs/tags/1.10.1

所有的魔力都在 refspec 中。因为推送通常会为您扩展 refspec，它会尝试匹配任何适合您当前 head 设置的内容，这里我们手动将其设置为: ***请不要将任何内容推送至原点的标签引用 1.10.1。*** 也就是我们现在知道的删除遥控器上的标签。

是的，我肯定是想当然地不去查看 *refspecs* 。我很高兴我做到了。有趣的是，理解这里发生的事情也解释了一些其他的错误，以前当我试图推或拉东西时，这些错误是没有意义的。下次我点击这些，也许我会写点什么。希望这也能帮到你。

#### 进一步阅读

*   关于 git-scm 的 Git 内部章节。涵盖我提到的一切，然后一些！总体资源丰富。

#### 注

我没有介绍在 *refspec* 格式中的+是什么意思。它首先是可选的，并告诉 git 更新引用，即使它不是一个*快进*。我不想深究这意味着什么，也许改天再发一篇文章。