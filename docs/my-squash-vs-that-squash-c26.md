# 我的壁球对那个壁球

> 原文：<https://dev.to/hawicaesar/my-squash-vs-that-squash-c26>

最近，为了成为一名优秀的初级开发人员，我提高了我的培训赌注。我被安排在一个重视约定集的团队中，会对拉取请求进行评论并标记为关键请求(这意味着必须立即对其进行分类)。在我们的维基上找不到的一个不成文的规则是挤压提交。这很有趣，因为团队领导提到我们的提交不应该超过 5 次。一位同行开发者 JK 对我的 pull 请求发表了评论，告诉我我违反了 3
提交限制。因此，我去了我们的老朋友谷歌。在打开和关闭几个堆栈溢出标签，浏览 git 帮助页面后，我准备尝试一些东西。我决定试验一下我以前做过的一个项目。我不想以任何方式搞砸我已经拥有的项目、分支和提交。(安全起见，小戴夫害怕)

我将目录更改为我拥有的 bucketlist 应用程序，然后做了一个`git log`来查看我拥有的最后几个提交。然后我输入了下面的

```
# Reset the current branch to the commit just before the last 2:
git reset --hard HEAD~2

# HEAD@{1} is where the branch was just before the previous command
# This command sets the state of the index to be as it would just
# after a merge from that commit:
git merge --squash HEAD@{1}

# Now commit those squashed changes. The commit message will be helpfully
# pre-populated with the commit messages of all the squashed commits:
git commit 
```

Enter fullscreen mode Exit fullscreen mode

这对我很有效。

我的另一个朋友 BT 后来加入了我们，他的任务是在我们的项目中配置持续部署，他想要打开一个拉请求。我站在他旁边，听他解释他做的噩梦，但设法让事情运转起来。JK 参加完一个会议，坐在 BT 旁边，问他在提出拉取请求时是否有任何问题。BT 提到他不会打壁球。JK 说这很简单，打开他的笔记本，JK 在这台笔记本上输入了下面的命令。我决定躺下，因为我还是新人。

```
git reset --soft HEAD~3 
```

Enter fullscreen mode Exit fullscreen mode

然后他解释说，这个数字代表了您想要挤压的提交的数量。在上面的例子中，3 表示您想要挤压的最后 3 个提交。

我很好奇，我和 JK 讨论了上面的命令，他解释了他的理由。

1)我的问题是哪个命令更容易？或者这是一个不该问的问题？这些命令依赖于项目吗

我喜欢 JK 使用的命令，尽管它满足于找到相同问题的不同答案。

挤压提交时使用什么？

感谢 Mark LongAir 关于堆栈溢出的[回答](https://stackoverflow.com/a/5190323)