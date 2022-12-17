# 故意的饭桶

> 原文：<https://dev.to/sdball/deliberate-git-nlf>

你好互联网！这是我以博客形式发表的演讲“故意的饭桶”。

还有我在钢城 Ruby 2013 上展示[刻意 Git 的视频。](https://vimeo.com/72762735%5D)

如果你想看幻灯片，它们在演讲台上:[刻意 Git - Slides](https://speakerdeck.com/sdball/deliberate-git) 。尽管我强烈建议，如果人们想阅读这篇演讲，就把它指向这篇博文，而不是在线观看。

特别感谢 PhishMe 派我到钢城 Ruby 来做这个演讲！

# 去吧

许多团队将 Git 视为沮丧的来源。一个痛苦的提醒，橡胶需要满足道路，以实现收支平衡，让我们的客户满意。他们认为 Git 只是一种将代码从开发机器传输到生产服务器并保持每个人同步的机制。

但是我想 Git 做更多。分布式意味着 Git 让我们有机会做一些真正令人惊奇的事情。它允许我们在不中断流程的情况下在本地快速提交，然后允许我们将这些提交重写为一个有凝聚力的故事，并与我们的团队共享。

如果你现在专注于把更多的信息放入你的回购，当你以后有问题时，你会看到惊人的回报。让我们从 Git 存储库最基础的部分开始。

# Git 提交

谁不喜欢 git 提交？提交是惊人的！我喜欢说 git 提交就像是发给未来开发者的电子邮件，但这并不完全准确。他们更棒！

## 永远提交

除非您删除它们，否则 git 提交会永远持续下去。如果你去探索像 Rails 这样长时间运行的代码库，你会发现提交消息已经有十年的历史了。

## 提交极具搜索性

可以通过多种方式轻松搜索提交消息。您可以通过消息体、代码变更、接触的文件、作者、日期等等来查找提交。

## 每个人都可以提交

团队中的任何人对存储库所做的每一次提交都可以被克隆存储库的任何人立即获得。所以让他们好起来。你现在提交得越好，你将来的改变就越容易。

# 提交时坏

提交可以是前一个程序员交给你的问题的答案。它们也可能是一些令人惊讶的强大倾向的来源，将你的头引到桌子上。

几年前，我正在开发一个应用程序。我们日以继夜地工作，以便在新闻发布截止日期前准备好部署这个新应用程序。我们有不断变化的业务方向，不断调整的页面布局，偶尔完整的网站重新设计，以及许多我们应该与之交流或提供的 API。

总之就是一塌糊涂。

举一个具体的例子:我们一直使用外部 API 来跟踪网站上兴趣表单的新销售线索。好吧。

我正在总结一个使用该 API 的特性。太好了！

不幸的是，当我合并我的更改时，我发现在 master 中，lead tracking API 已经被一个新服务的新 API 完全取代了。

不不。:-(

当时是深夜，只有我一个人在那里，所以团队里没有其他人可以让我立即交谈。

我检查了我关于这个项目的电子邮件，但是我找不到任何提到如此大的变化的东西。我查看了最近几次商务会议的笔记(都是在最近两天内)，仍然一无所获。

但是啊哈！我们是一个使用 Git 的全新项目！我敢打赌，围绕代码更改有一个很好的提交消息。当然啦！到 git 日志！

```
Forgot to update form action for new lead engine 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这没什么帮助，但我们的方向是对的。也许我们需要更进一步。

```
Switched to new lead engine. 
```

Enter fullscreen mode Exit fullscreen mode

是的……是的，我知道。但是为什么呢？为什么？现在是时候让我的头碰到桌子了。

提交消息不仅没有帮助，而且很容易有帮助！

考虑这个选择:

```
Replace lead tracking Foo with Bar

Foo's API has dropped 5 out of the last 27 emails since we started keeping our
own logs. Their support is not responding.

Fred and I decided to drop in Lead Engine B after checking alternatives (see
wiki:LeadEngineResearch). 
```

Enter fullscreen mode Exit fullscreen mode

那有多有用？更有用，对吧？

我们来分解一下。

# 主语

```
Replace lead tracking Foo with Bar 
```

Enter fullscreen mode Exit fullscreen mode

主题行是清晰的，命令式的，简短的。

如果我看到一个只显示主题的提交列表，那么我仍然很清楚这个提交做了什么。

许多 Git 工具和命令只显示提交的主题。所以让它们简短直接会让它们更容易理解。

# 正文

```
Foo's API has dropped 5 out of the last 27 emails since we started keeping our
own logs. Their support is not responding.

Fred and I decided to drop in Lead Engine B after checking alternatives
(see wiki:LeadEngineResearch). 
```

Enter fullscreen mode Exit fullscreen mode

该主题行由一个详细的主体支持，并对其进行了进一步的解释。

主体告诉我们提交做了什么，主体告诉我们为什么。

这就是提交应该做的。陈述变化，然后解释变化。

# 好提交主题

一个好的提交主题是用现在时态和命令形式写的。

## 现在时态

为什么是现在时？因为它们描述了提交做了什么，而不是它做了什么。Git 提交应该用现在时写，因为它们会发生多次。

当你写的时候它们就发生了。当你把它们合并到另一个分支时，它们就发生了。当其他人取消他们回购的新承诺时，就会发生这种情况。当有人把它们摘到树枝上时，它们就会发生。

重写使用过去式书写主题的提交是无意义的。它引入了认知失调，让编辑你的提交历史感觉怪怪的。这可能看起来很傻，但这种脱节的感觉会微妙地推动你永远不编辑你的提交。

## 命令

为什么提交主题要写成命令？因为提交是存储库的待办事项。

提交说:“嘿，饭桶！这里有一组要做的改变。”

Git 说:“太好了！我会的，如果我想不出什么办法，会告诉你的。”

提交不是历史日志。它们描述了如果您的提交被添加到存储库中会发生什么。它们是对回购应该做什么的描述。

# 好提交尸体

## 可以不拘小节

提交主体不必使用简洁的语言写成现在时态命令。事实上，非正式地写通常更好。

## 形容“为什么？”

提交主体描述的是待办事项，而不是待办事项本身。为什么我们要翻转钻头或我们正在做的事情？

## 是给你团队的消息

提交主体是您放下并实际与您的团队交流的地方。解释事情，就像你在写总结你的变化的信息一样。因为这就是你正在做的！

# 写得好的提交

我认为提交写得很好，因为它解释了两件事:

## 代码更改的作用

提交应该解释代码更改做了什么，而不一定是代码做了什么。提交是我们旋转的点。这是我们轻松捕捉之前的代码和之后的代码，并用解释将它们粘在一起的唯一机会。

## 为什么变革是必要的

提交应该解释为什么更改是必要的。我们做了哪些不同的事情，需要这种承诺来实现我们的目标？正在为一个功能奠定基础。我们在修复一个 bug 吗？我们在重构吗？这些是需要了解的关于代码变更的重要事情，我们仅仅通过检查代码本身是无法得到的。

但是不要觉得仅仅局限于这两条信息。将尽可能多的关于代码的知识放入提交消息中。更多信息的一些示例如下:

## 考虑备选方案

你是如何得出这个解决方案的？也许将连接数设置为 500 可以解决流量高峰，或者这可能是你在煞费苦心地研究和测试应用程序后得出的最佳设置。谁知道呢？你知道。是你在改密码！所以写下来！

## 潜在后果

会有什么后果？谈论代码更改可能产生的影响。有什么我们应该注意的吗？需要注意什么吗？如果有，写下来。

# Git 提交像注释吗？

此时，你可能会想:“但是斯蒂芬。这听起来非常像用注释记录代码。过多的评论不是一个坏主意吗？”

是的，大量的注释(有些人会说任何注释)是一个坏主意，因为注释很容易与代码不同步。

```
# set x to 5
y = 3 
```

Enter fullscreen mode Exit fullscreen mode

也许把 y 设为 3 确实会把 x 设为 5。或者也许那个评论只是不同步。我们无法知道！所以是的，小心对待评论。

但是，这种差异永远不会在 git commit 中发生！

# Git 提交总是与代码同步

提交与他们正在谈论的代码变更联系在一起。当代码改变时，一个新的提交消息将自动进入并取代它的位置。

这很好，因为它允许您编写关于更改的文档，而不必担心文档会过时。它将和它所谈论的代码持续的时间一样长。

提交消息也不会弄乱代码，但是如果你需要的话，它们就在你身边。

# 编码流程呢？

现在你可能会说:“但是，斯蒂芬。写得好的提交听起来很棒，但听起来工作量也很大。你真的希望我在编码的美妙流程和提交的令人崩溃的烦恼之间来回跳跃吗？”

不要！那太可怕了！

## WIP 编码时提交

如果您正在处理本地提交，那么您的提交就是您的事情。直到他们进入公(产)录，为所欲为！当我不想中断我的流程，但是我知道我想要记录一个变更时，带有简写引用的“WIP”是我的 goto commit。

## 频繁提交

即使是在编码的过程中，进行大量的小提交也是很重要的。组合提交非常容易，从大的代码变更中进行原子提交比较困难，而将大的提交分解成原子提交非常困难。因此，宁可选择较小的提交，稍后再进行组合。

## 重写您的提交

当你在编码过程中遇到瓶颈时。切换到写模式，重写、删除、合并和/或重新排序你的提交。把它想象成红绿重构；但是编码，提交，重写。编写代码，提交代码，然后重构提交的代码。

如何重写提交？Git 有一个简单的命令可以做到这一点！

# Git Rebase

不，不，别跑！会好的！(对你们中的一些人来说，危险信号已经升起。)

有些人可能不知道:rebase 是一个 git 命令，它允许您修改已经提交的内容。这是非常强大的，但是如果您修改已经是其他人历史的一部分的提交，这也是非常令人头疼的。

但是不用担心！我们将精确、谨慎、安全地使用 git rebase。我们还将以一种适用于任何团队工作流的方式使用它。

魔咒是:`git rebase -i`

`-i`代表互动。而`git rebase -i`是 git 的杀手级特性之一。

这给了你快速破解代码的能力，同时也给了你的团队很好的提交信息。如果您希望遵循良好的提交消息的路径，并且仍然希望以良好的流程完成代码，那么学习这个命令是绝对必要的。

`git rebase -i`允许您:

*   重写提交
*   删除提交
*   合并提交
*   重新排序提交

(它让你做得更多，但这是我们现在要担心的。)

您编写您的速记提交，然后使用`git rebase -i`来处理您自己的本地提交，并在将它们推到任何地方之前重写它们。就好像你只写过惊人的提交。

利用我们所掌握的`git rebase -i`的力量，我们可以进行 36 次提交来实现一个特性，其中大多数都有一个匆忙起草的消息。然后用魔法修剪杂草，最后只有 5 个写得很好的提交与我们的团队分享。

```
$ git rev-list master..feature | wc -l
37

$ git rebase -i 1123e81
[magic happens] (ﾉ◕‿◕)ﾉ*:･ﾟ✧

$ git rev-list master..feature | wc -l
5 
```

Enter fullscreen mode Exit fullscreen mode

# 使用 git rebase -i

当你准备好开始重写你的时候:

*   使用`git log`查找您想要更改的第一个提交之前的提交
*   运行`git rebase -i [that commit's hash]`

例如，假设`3a049ba`是我们三个 WIP 提交之前的稳定提交，我们希望将这些 WIP 提交合并成一个内聚的消息。

```
# Find the stable commit hash
$ git l

* 193bn13 WIP switch fizz to bazz
* 6a0b73a WIP support fizz
* aa02d1e WIP Start to hack out foo
* 3a049ba Allow creating users via JSON

# Run git rebase -i with that commit's hash
$ git rebase -i 3a049ba 
```

Enter fullscreen mode Exit fullscreen mode

Git 将在您的编辑器中打开一个这样的屏幕

```
pick aa02d1e WIP Start to hack out foo
pick 6a0b73a WIP support fizz
pick 193bn13 WIP switch fizz to bazz

# Rebase 31049ba..193bn13 onto 31049ba
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom. 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很复杂，但是我们只关心这些部分:

```
pick aa02d1e WIP Start to hack out foo
pick 6a0b73a WIP support fizz
pick 193bn13 WIP switch fizz to bazz

# p, pick = use commit
# r, reword = use commit, but edit the commit message
# f, fixup = like "squash", but discard this commit's log message
#
# These lines can be re-ordered; they are executed from top to bottom. 
```

Enter fullscreen mode Exit fullscreen mode

为了将 WIP 提交重写为一条消息，我们告诉 Git:

*   改写第一次提交
*   只保留最后两个的更改(而不是消息)

像这样

```
reword aa02d1e WIP Start to hack out foo
fixup 6a0b73a WIP support fizz
fixup 193bn13 WIP switch fizz to bazz 
```

Enter fullscreen mode Exit fullscreen mode

保存并关闭该文件后，git 将打开另一个编辑器，其中包含第一次提交的消息。在我们重写它之后，git 将创建新的提交消息，它合并了三个 WIP 提交。

```
Add foo feature with bazz support

The ability to foo has been requested by various
users. This implementation will allow foo with
support for the bazz protocol.

At first we thought that the fizz protocol would
be a good fit, but that wasn't the case when we
tested it against over 10,000 interactions. We
opened up an issue with the fizz API.

- The bazz protocol: http://example.com/bazz
- fizz api failure issue: http://example.com/issue/12 
```

Enter fullscreen mode Exit fullscreen mode

# 挖掘历史

“好了，斯蒂芬，现在我可以写很棒的提交了。但是如果我再也找不到他们，那又有什么用呢？”

没用的！一点好处都没有。幸运的是，有很多工具可以筛选你的 git 历史。GUI 工具最容易上手，但是我将向您展示一些命令行选项，您可能会发现这些选项很有用。

```
# list commits newest to oldest
git log

# list commits with their diff
git log -p

# only show commits whose *messages* contain the string
git log --grep="commit contents"

# only show commits whose *code change* contains the string
git log -S"diff contents"

# only show commits for the given path or file
git log -- path/or/file

# show all the lines of a file and their current commit
git blame FILENAME 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用这些命令在 Github 的现有代码库中找到一些非常有趣的东西。比如说 Rails 代码库。

```
# Run these against rails/rails

# Find the oldest commit
rails/rails git log --format="%H" | tail -1 | xargs -L 1 git show

# Find commits with a magic word in them
rails/rails git log -S"xyzzy" -p

# Find commits that made people happy
rails/rails git log --grep=":heart:"

# Find commits that made people sad
rails/rails git log --grep="wtf" -p

# And find interesting people
rails/rails git log --grep="metz" 
```

Enter fullscreen mode Exit fullscreen mode

# 让我们结束这一切吧

你的代码只能说明它现在在做什么。你不能只看一个方法，而看它的历史:考虑过的替代方法，已经过时的算法，被替换的简单代码，或者被重构的复杂代码。

没有掌握这些知识是一个巨大的损失。你在代码中投入了大量的工作，所以尽可能多的把它放到回购中，这样每个人都能受益。

编写最后的 Git 提交消息，就像你在给你的团队写一封总结你所做的变更的电子邮件一样。因为你就是！捕捉这些细节。

```
# It is exactly like writing an email to the future!
$ git log --format=email 
```

Enter fullscreen mode Exit fullscreen mode

就像《流言终结者》中的亚当·沙维奇说的:“科学和胡闹的唯一区别就是把它写下来。”