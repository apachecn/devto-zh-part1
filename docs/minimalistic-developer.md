# 简约开发者

> 原文：<https://dev.to/subbramanil/minimalistic-developer>

## 灵感

写这篇博客的最初灵感来自于[詹姆斯·胡德](https://dev.to/jlhcoder)在 dev.to 上写的一篇[帖子](https://dev.to/jlhcoder/the-power-of-the-todo-list)。在那篇博客中，詹姆斯提到他正在用“文本文件”做待办事项，我当时就想到了“老派”。我还添加了一条关于我如何使用 evernote 来达到类似目的的评论。几周前，我的一位资深同事(我非常尊敬的人)给了我一些建议。

他的建议是，

> 不要太依赖应用程序，因为你可能永远不知道你将来会在哪里工作。

我学长的建议让我再次阅读詹姆斯·胡德的博客。当我阅读博客和评论时，我开始重新思考我的工作流程，并有意识地监控我每天使用的工具和使用方式。我在工作中使用 Macbook Pro(2013 年末)。作为一名 Android 开发人员，我的主要开发工具是，

1.  Android Studio(显然)，
2.  [pidcat](https://github.com/JakeWharton/pidcat) 用于测井、
3.  [安卓截图工具](https://github.com/mortenjust/androidtool-mac)

除此之外，这些是我每天安装和使用的应用程序，

1.  Evernote 用于记录和跟踪我的工作
2.  **版本控制的源树**
3.  杰森(Jayson):一款用于查看 JSON 文件的 Mac 应用
4.  **Visual Studio 代码**或**升华文本**来查看文本文件

除了我的 macbook，我还远程 ssh 到我们运行 ubuntu 14.04 的构建和发布服务器上。我并不主动在那些机器上写代码，但是我偶尔会写脚本。

## 想法

这个想法是通过采取极简主义的方法来减少应用程序的依赖性，并用简单的解决方案来解决问题。

我采纳了詹姆斯·胡德(James Hood)关于使用文本文件作为待办事项列表的想法，以及他关于使用私人“git”repo 来备份笔记的评论。

所以基本上，我在工作流程上做了两个简单的改变。

*   使用支持 **markdown** 的文本文件代替 Evernote
*   使用 **git 终端命令**代替 Sourcetree

之前我为我的博客创建了一个回购，目录结构如下，

[![blog directory structure](img/c5b56b08d0c7754288a07074f3375b95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R1xf2V2G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/my-blogresources/screenshots/blogs_dir_structure.png)

我扩展了这个想法，并创建了一个如下所示的目录结构，

[![dev notes directory structure](img/7bf57b8f620a53b94609b167fa2ba480.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fT-c1Vp8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/my-blogresources/screenshots/dev_notes_dir_strucutre.png)

现在，我所有的笔记、博客和工作日志都在我的私人储存库中的一个地方。我所需要的是一台装有 git 的机器和互联网来访问它们。

我之所以选择使用 *markdown* 而不是纯文本文件，是因为 markdown 使得将来查看笔记更加容易。当你读完这个博客，你会明白我的意思。

## 制作笔记

一次将我所有的笔记从 Evernote 迁移到 Git 存储库是一项痛苦的任务。所以我遵循这种方法。

1.  任何新票据都将被写入*降价*并提交给回购。
2.  每当我从我的 *evernote* 中引用任何笔记时，我都会以减价的形式将它们复制到我的回购协议中。(这是一个缓慢的过程，但我相信需要一段时间才能完成)

## 跟踪工作

在*工作日志*目录下，我创建了一个主待办列表降价文件， **00 Todo 任务列表. md** (名称以 00 开头，以便在排序时位于目录结构的顶部)，还创建了一个子目录 **resources** 来添加额外的资源文件，如截图、日志文件等。,

顾名思义，主待办事项列表是添加/删除/审查任务的公共参考点，也有助于记下我工作时出现的任何新想法。

我开始以“date_day.md”的格式创建 markdown 文件(与在一些网站写博客的风格类似),因此在我工作两周后，它看起来如下。

[![Work Log File Structure](img/ba2df2a43674eb708d65c1a1cc9f2cd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L8Z1zoG_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/my-blogresources/screenshots/work_log_complete_structure.png)

我通常会在头天晚上或一大早就计划好我的一天。任务可能来自*主待办事项列表*或基于分配的会议/电子邮件/JIRA 任务。每个降价文件在创建时都遵循以下格式。

```
 # Plan for the day
 - [ ] Task A
- [ ] Task B
- [ ] Task C
 -------- 
```

Enter fullscreen mode Exit fullscreen mode

一旦我添加了当天的计划，我就提交我的更改。

```
git add Work Logs/file.md
git commit -m "Adds Plan for the day"
git push 
```

Enter fullscreen mode Exit fullscreen mode

当我开始一天的工作时，我会根据优先级对任务进行优先排序，并从优先级最高的任务开始工作。出现的任何新任务都将被添加到列表底部。在任何时候，只有一个任务(当前任务)会被标记为**粗体**(使用 markdown 语法)，以保持我的关注。

```
 # Plan for the day
 - [ ] Task A
- [ ] **Task B**
- [ ] Task C
- [ ] Task D
 -------- 
## Task B
 - Task description/Link
 - Notes related to tasks
 - To do list of actions 
```

Enter fullscreen mode Exit fullscreen mode

我遵循一个严格的规则，不跳到另一个任务，除非它是最优先的任务或时间敏感的。否则，在完成或到达当前任务的检查点之前，我不会开始另一个任务。在跳转到下一个任务之前，我用完成状态更新任务，以及是否有任何未完成的任务。

```
 ## Task B
 - Task description/Link
 - Notes related to tasks
 - To do list of actions
 - Actions taken
 - **Actions Pending** 
```

Enter fullscreen mode Exit fullscreen mode

我承诺如下。

```
git add Work Logs/file.md
git commit -m "Completes Task-D"
git push 
```

Enter fullscreen mode Exit fullscreen mode

我重复这些步骤，直到一天结束。最终它会变成这样。

```
 # Plan for the day
 - [x] Task A
- [x] Task B
- [ ] **Task C**
- [x] Task D
- [ ] Task E
 -------- 
## Task B

...
 -------- 
## Task A

...
 -------- 
## Task D

...
 -------- 
## Task C
 - Task description
 - todo list of actions
 - **Action pending** 
```

Enter fullscreen mode Exit fullscreen mode

在一天结束时，我会回顾已完成的任务和未完成的任务。最终的工作日志可以只包含当天完成的任务，这样便于将来查看已完成的任务。根据进度和优先级，所有未完成的任务要么放到第二天，要么放到主待办事项列表中。例如，*任务 C* 可以转到第二天，而*任务 E* 可以转到主待办事项列表。

当移动未完成的任务时，我复制任务到目前为止的进度。所以我真的不需要再参考前一天的笔记。同样，前一天遗留下来的未完成的任务有助于第二天优先处理这些任务。

## 这种方法的好处

我从这种方法中得到的最明显的好处是，我不再需要几个应用程序。只要我能上网，我就可以在任何地方查阅我的笔记。

```
# Gradle Notes

## Gradle Wrapper
 - Wrapper is a small shell script that can download gradle if itsn't installed and can run tasks.

## Gradle Daemon
 - Runs gradle in the background.
- Gradle creates separate JVM to run tasks. 
- Gradle daemon re-uses the JVM created earlier. 

## Tasks
 - Self contained piece of work
- Performs operations in isolation
- Can depend on other tasks
- Can have input & output 
```

Enter fullscreen mode Exit fullscreen mode

回顾我的工作日志，很容易看到那天完成了什么，我采取了什么行动。

[![Sample work log](img/53ab5e33cf4120052b89f8bbbaf2e0d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TPSySnRC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/my-blogresources/screenshots/work_log_sample.png)

这个机会也帮助我每天使用 git 命令来掌握它们。

## 免责声明

1.  在从移动设备编辑笔记方面，您可能会失去一些移动性。如果你用手机做笔记，这种方法可能没什么用。
2.  当谈到通过命令进行版本控制时，有一个学习曲线。对有些人来说，这似乎并没有提高你的生产力。但是您可以安装扩展/插件来自动完成命令。

## 很少提示

*   我使用带有 *markdownlint* 扩展的 **Visual Studio 代码**，这有助于我避免 markdown 格式错误。
*   我还利用了 Visual Studio 代码中的**用户片段**特性。例如，这里有一个我用来在 markdown 文件中插入代码块的片段。

[![User Snippets](img/200f1107acaf10cbbe3ef15cc4c2a9de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uW1fIRDj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/my-blogresources/screenshots/user%2Bsnippets.png)

请随意分享你的想法。