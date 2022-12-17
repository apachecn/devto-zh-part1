# 2017 年学习 Rails

> 原文：<https://dev.to/fdocr/learning-rails-in-2017>

今年到目前为止是超现实的&至少可以说是忙碌的，我们基本上已经走过了一半。去年年底，我开始了一份新工作，由于种种原因，我认为这份工作很有挑战性。可以说是最重要的一个:我将支持一个构建在 [Ruby](http://ruby-doc.org/) (在 [Rails](http://rubyonrails.org/) 上)的大型代码库。过去，我也曾因[那些垃圾的编程语言](https://www.xkcd.com/1312/)而犯罪，我对这些语言(包括 Ruby)一无所知。**公开道歉**写在这篇博文里，有我学习这项神奇技术的个人经历的提示/笔记/评论。

## 向前两步，绝不后退一步

Ruby 和 Rails 不是闪亮的新玩具，不是今天。在这次演出之前，我在 NodeJS 工作了大约一年，并爱上了它。老实说，我并不期望用一种新的语言和框架重新开始。尤其是考虑到最近科技浪潮的方向，这似乎不是一个显而易见的方向。

为什么人/公司会选择 Java 或者。网？很大程度上是因为他们寻找一个久经考验、功能全面的平台，并提供企业支持。为什么选择 NodeJS，Go，Elixir，Clojure？(你明白了)[因为我想跑得更快](https://www.youtube.com/watch?v=riBA-FsJJmY)，当然还有其他原因。但有人可能会说，性能是大多数采纳者在探索这条道路时所追求的。尽管他们可能不需要超快的速度，而且大多数情况下他们**并不绝对需要**这种性能增益。

那为什么是 Ruby 和 Rails 呢？因为[为什么不是](http://i1.kym-cdn.com/entries/icons/facebook/000/007/786/why-not-meme.jpg)，而只是部分。平台成熟度、开发者快乐/生产力和*开源性*(如果这个术语有意义的话)组成了一杯鸡尾酒。我们都想把事情做好，对吗？我很高兴也很自豪地将 Ruby 和 Rails 添加到我的工具箱中，它已经并将继续帮助我完成工作。

## 入门

如果你正在寻找 Ruby on Rails 的入门指南[，这里是你应该去的地方](http://guides.rubyonrails.org/getting_started.html)。我的看法是，如果你阅读官方指南中的**代码和**入门教程，你会明白大约 50%的 Rails 是怎么回事。另外大约 30%的人住在来自 guides.rubyonrails.org[的所有其他导游那里。](http://guides.rubyonrails.org)

是的，我相信~你需要了解的关于 Rails 的 80%的内容都可以在一个网站中找到！这最后的 20%是帕累托法则的全部，你不得不付出一些努力来跨过这个门槛。

对于刚刚开始或打算开始的人来说，这里有一些概念让我茅塞顿开！Moment (同时涉及 Ruby 和 Rails):

*   “不是所有的东西都是普通的红宝石，但是**所有的东西都是对象**”。对于有经验的 Ruby 开发人员来说，这似乎是显而易见的，但对于初学者或有老派静态类型语言经验的编码人员来说，这可能并不明显。例如`1.week.ago`是[与 Rails](https://rubygems.org/gems/activesupport/) 打包在一起的一个‘扩展’，你需要显式地导入它以便在 Rails 项目之外使用。而[整数本身就是对象](http://ruby-doc.org/docs/ruby-doc-bundle/Tutorial/part_01/objects.html)，这就是为什么你能够在 Java 程序员所知的[原始数据类型](https://stackoverflow.com/a/10430634/3462026)上调用函数。
*   在介绍性教程中，你会听到很多关于**约定优于配置**的内容，经过一点实践，你将会深入理解其中的大部分内容。一开始我很纠结的一些问题是:
    *   文件名(控制器、模型、视图、助手等)通常是 snake_cased，但是它们的实现(代码中的类名)是 CamelCased。它们都是单数，因为在使用时它们将代表**一个对象**(即`User.new`)。
    *   模型关联名称以复数形式声明，例如在`user.rb`中，您可以写`has_many :books`来与`book.rb`关联。**除非**它们是一个`belongs_to`或`references`关联，这是有意义的，因为它们指的是一个**单记录**。书本模型将显示为`belongs_to :user`。
    *   形式和路线可能看起来很复杂，尤其是在嵌套的时候，比如`form_for [@article, @article.comments.build]`。就个人而言，它们仍然很棘手，尤其是当我想在表单外实例化一个到自定义路由路径的链接时。为此我仍然做了一些尝试和错误，或者从 StackOverflow 复制&粘贴[的老把戏。](https://twitter.com/thepracticaldev/status/705825638851149824)
    *   我的建议还是按照[入门指南](http://guides.rubyonrails.org/getting_started.html)进行实验和追踪。除非你*让自己*偏离一点点(100%有意双关)，否则你不会面对传统的轨道怪癖。
*   `yield`命令类似于“执行收到的块”。这让我花了几分钟重新阅读不同的解释。当声明一个有 2 个参数的函数时，它实际上可以接收 3 个。这个额外的(可选的)参数是一个“类似回调”的块。例如，当您用`array.each { |i| puts i }`迭代一个数组时，数组的底层实现迭代它的元素，并将该元素“让给”您发送的回调函数。来自 NodeJS 背景，使用术语“*回调*的简化解释对我来说是有意义的。如果你好奇的话，这个 [StackOverflow 的回答](https://stackoverflow.com/a/3066939/3462026)会更详细。

## 书签

对我来说，官方指南之后的下一步是开始寻找某些主题的深度。那种类型的知识只来自有经验的程序员。谁有我们寻求的宝贵知识？很高兴你问了，我的一些书签是(排名不分先后):

*   RubyTapas :对许多不同主题有着深刻见解的短片。我真的很喜欢[系列](http://www.virtuouscode.com/2014/04/30/learn-advanced-rake-in-7-episodes/)(有点像 RubyTapas 之外的，但作者相同，主题重叠)。
*   tenderlovemaking.com:你会在这个网站上发现你不能看不见的东西——“温柔地做爱”(͡ ͜ʖ ͡)
*   Schneems 的博客:性能是这里反复出现的话题。不管你在什么平台上编码，谁没有对速度的需求？
*   [官方文件](http://api.rubyonrails.org/):学习一个新的框架意味着你会不断地谷歌“X 是如何工作的”。我很抱歉地说，有时 [API 码头](https://apidock.com/rails)没有我正在寻找的最好的最新信息。虽然仍然有帮助，但是我倾向于先检查`api.rubyonrails.org`结果。
*   [ConFreaks](https://www.youtube.com/user/Confreaks/playlists):2017 年、2016 年以及之前的所有 RailsConf 会谈都在里面，很棒的材料。[多宝鱼 5 号:我真不敢相信这不是本地的！作者:山姆·斯蒂芬森](https://www.youtube.com/watch?v=SWEts0rlezA)绝对是迄今为止给我印象最深的个人最爱(这是来自一个 iOS 开发者😉)
*   已故的吉姆·威里奇(Jim Wei rich)的任何言论:那段视频太棒了，我迄今为止从他那里看过的所有其他视频也是如此。他去世多年后，他的遗产也影响了我的生活。[非常感谢吉姆](https://github.com/jimweirich/wyriki/commit/d28fac7f18aeacb00d8ad3460a0a5a901617c2d4)。
*   当然是播客:我已经听了几集[5by 5](http://5by5.tv/rubyonrails)&[ruby rogues](https://devchat.tv/ruby-rogues)了，确实很有趣。

我很想听听我错过的其他有趣的来源，我相信有很多。还有必要提一下:至少浏览一下你常用的 gems 的自述文件会大大降低你对“ *Rails 在引擎盖下施展了很多魔法”*的看法。

这是开源的人，利用能够阅读一些行业中最好的程序员的代码。

## 日常便签

我经常查找一些小片段，因为它们很有用(我懒得去记它们):

*   [回滚 N 次迁移](https://stackoverflow.com/questions/3647685/how-to-rollback-a-specific-migration/3647820#3647820):

```
rake db:rollback STEP=1 
```

Enter fullscreen mode Exit fullscreen mode

*   [在 Rails 控制台上执行自定义 SQL 语句](https://stackoverflow.com/questions/22752777/how-do-you-manually-execute-sql-commands-in-ruby-on-rails-using-nuodb/30474027#30474027):

```
results = ActiveRecord::Base.connection.execute("foo") 
```

Enter fullscreen mode Exit fullscreen mode

*   [使用 tee](https://stackoverflow.com/a/12350076/3462026) 将 STDOUT 记录到文件中(我猜只有 UNIX):

```
STDOUT.reopen IO.popen "tee stdout.log", "a"
STDERR.reopen IO.popen "tee stderr.log", "a" 
```

Enter fullscreen mode Exit fullscreen mode

*   [Docker 记录标准输出修复(刷新)](https://stackoverflow.com/a/29998780/3462026):

```
// From what I hear this comes with a performance penalty due to costly I/O
STDOUT.sync = true 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

每种语言和框架都有一个学习曲线，没有例外。我确实觉得 Ruby 和 Rails 对新手来说都很温和，如果你懂 JS/Java/Python(就像很多 CS 毕业生甚至高中生有时会做的那样)，你可以很快上手并运行起来。

[Turrialba](https://visualcosita.xyz/turrialba) 是我在学习阶段建立的一个网站。我重写了其中的部分，可能有 3 到 4 次，我决定最近出版它。这是一个科学怪人的实验，我并不为 UX 感到骄傲，但请随时查看并给我反馈。不要太挑剔，我知道它的边缘很粗糙。**补充说明:**rails 应用程序是基于我在[之前写的一篇博文](https://visualcosita.xyz/post/publishing-services-using-docker-compose-and-nginx-with-https/)*自我表扬*而编写的，并作为一项服务松散地运行

在我看来**使用框架**是**学习框架**的唯一途径。玩得开心，无论这些天你在做什么，Pura Vida！

这篇文章最初发表于[visualcosita.com](https://visualcosita.com/learning-rails-in-2017/)