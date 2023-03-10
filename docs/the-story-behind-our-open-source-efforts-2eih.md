# ★我们开源努力背后的故事

> 原文：<https://dev.to/freekmurze/the-story-behind-our-open-source-efforts-2eih>

*本帖最初发表于[24daysindecember.net](https://24daysindecember.net/2017/12/23/the-story-behind-our-open-source-efforts/)T3】*

你可能从未听说过我的公司。我们专门为客户创建 Laravel 应用程序。我们的团队相当小:我们只有 6 名开发人员和一名经理。乍一看，我们只是一家网络代理公司，就像许多其他公司一样。但是有一点让我们公司与众不同:我们有一个开源优先的政策。我们试图尽可能地创造和贡献开源。

目前我们在 GitHub 上有大约 170 个开源库。我们的软件包已经被下载了近 800 万次。它们每月被下载 80 万次。Git awards 是一个网站，它根据 GitHub 组织的回复的星级数量对其进行排名。在过去的一年里，我们设法在 Git Awards 的全球 PHP 开发者排行榜上获得了第三名。是的，我知道明星的数量不是一个重要的指标，但对于一个小团队来说，这仍然是一个不错的成就。

这一切都不是一夜之间发生的。请继续阅读，了解我们开源努力背后的故事。

## 第一包

大约在 2007 年，当我开始为 Spatie 工作时，我敢于编写自己的框架。那时我正在做的项目非常小。提交一个简单的表单是这些网站应该做的最高级的事情。

几年后，我们的项目有了一点进展。我意识到编写和维护我自己的框架不再有用了。我听说了这个叫做 Zend Framework 1 的奇特的东西，并开始使用它。

让我们再往前跳几年。在 2012 年和 2013 年，我觉得 PHP 生态系统相当陈旧，我考虑过转向 Ruby 和 Rails 框架。但是后来撞上了 Laravel 4.0。富于表现力的语法和对开发人员快乐的极大关注立刻给我留下了深刻的印象。我用它做了几个项目，对它了解得越多，我就越喜欢用它。

那时，一个叫 Jeffrey Way 的人开始了他的下一个项目 [Laracasts](https://laracasts.com) :一个专门为 Laravel 服务的视频辅导网站。意识到他的视频可以极大地加快我的学习过程，我立即购买了终身订阅。

2014 年 3 月 4 日，Jeffrey 发布了一个名为“与 Travis 持续融合”的视频。我震惊了。 [Travis](https://travis-ci.org/) 和与 GitHub 的集成看起来太酷了，我想用它。在制作 Travis 视频的几个月前，Jeffrey 制作了一部关于包装开发的迷你连续剧[。这些视频激发了我的想法，我可以创建一个自己的包。](https://laracasts.com/lessons/package-development-101)

在当时的一个项目中，我们需要自动创建一个网站的截图。有了我在 Laracasts-video 中学到的东西，我开始做我的第一个包 [Browsershot](https://github.com/spatie/browsershot/tree/1.0.0) 。本质上，它只是一个围绕[幻象](http://phantomjs.org/) ( [当前版本](https://github.com/spatie/browsershot)使用无头 Chrome 和[木偶](https://github.com/GoogleChrome/puppeteer))的简单包装。

在户外工作很令人兴奋。每次下载计数器上升时，我都很激动，并且非常高兴其他人开始写关于它的文章。

## 更包！

在 Zend Framework 时代，我使用一个名为 Blender 的定制应用程序模板来启动所有客户端项目。这是一种小型合作医疗。除了传统的 CMS 功能之外，Blender 还可以做很多很酷的事情:从分析中提取数据，显示网站使用情况的图表，处理上传的文件，为用户订阅 MailChimp 列表等等

为了给新项目提供动力，我需要一个 Laravel powered 版本的 Blender，并开始从头开始创建它。我很快意识到我移植到 Laravel 的一些东西对其他开发人员也是有用的，并决定将这些功能导出到外部包中。

像[laravel-时事通讯](https://github.com/spatie/laravel-newsletter)、[laravel-媒体库](https://github.com/spatie/laravel-medialibrary)和[laravel-分析](https://github.com/spatie/laravel-analytics)这样的软件包相继问世。

我开始越来越喜欢包开发。我早期从软件包用户那里得到的积极反馈非常令人鼓舞。我一直认为编程纯粹是工作，但是因为编写别人也可以使用的代码非常有趣，所以我也在业余时间开始编程。

除了开发软件包，我还打磨了前面提到的名为 Blender 的[应用程序模板，并将其开源。](https://github.com/spatie-custom/blender)

目前，我们创建的每个新包都诞生在一个客户项目中。几乎在每个项目中，我们都会发现一些可以提取到它自己的包中的功能。

## 创建包的好处

创建包有很多好处，让开发开源代码的时间花得值。

在创建包的过程中可以学到很多东西。每个包装都需要精心制作。它提供了清晰易懂的语法。像 Laravel 一样，我希望我们所有的包都非常强调开发者的快乐。它们应该易于使用。他们应该有很好的文档。他们应该有明确的测试。想想其他人会如何使用你的代码会让你成为一个更好的开发者。

我们软件包的用户报告的问题和提交的 PRs 提供了另一个学习的机会。人们可以指出我们的软件包中的错误，并提出我们没有想到的有趣的新功能。

有时，当一个包获得一些牵引力时，你可以免费获得高质量的代码。我们的 [laravel-fractal](https://github.com/spatie/laravel-fractal) 包是一个开发者友好的包装，围绕着[联盟的 fractal 包](https://github.com/thephpleague/fractal)。我把基本功能编码成我自己需要的，并标记为 1.0.0。在接下来的几个星期里，我几乎每天都会收到一个请求，要求我给这个包添加另一个很棒的特性。现在它几乎支持联盟分形能做的一切。我认为那个包 90%的代码都是社区写的。

当然，我们也在[喂自己](https://github.com/spatie/blender/blob/master/composer.json#L39-L70)。我们自己的包被用于大多数项目。如果我们在一个项目中使用的一个包中发现了一个 bug，我们可以很快地修复它，并通过 Composer 的力量将这个修复分发给我们的其他项目。

还有商业利益。过去一年，由于我们的开源工作，我们获得了一些很酷的项目。在大多数情况下，这些新客户已经在使用我们的产品。尽管吸引客户不是我们开源努力的主要目标，但这确实是一个不错的副作用。

来自软件包用户的积极反馈也给了我足够的信心来开始[一个博客](https://freek.dev)并开始公开发言。在过去的几年里，我在几个当地的用户组(我正在我的家乡参与组织[的用户组)和世界各地的许多会议上发表过演讲。在过去的几年里，我有幸在华盛顿、阿姆斯特丹、东京、新德里、纽约、伊斯坦布尔，当然还有我的家乡安特卫普做了演讲。在所有这些场合](http://phpantwerp.be)，我见到了许多令人敬畏的开发者伙伴，包括经营[https://24daysindecember.net](https://24daysindecember.net)的[安德里亚斯](https://twitter.com/heiglandreas)。如果我呆在我的小泡泡里，这一切可能都不会发生。从事开源工作有助于联系社区。这听起来有点老套，但这并没有减少它的真实性。

## 腾出时间致力于开源

人们经常问创建包是不是很耗时。事实是这确实需要很多时间。为包本身创建代码、编写测试、编写文档以及发布消息都需要相当多的时间。

这仅仅是开始。当第一个稳定版本被贴上标签时，工作还没有结束。维护一个包，回应问题，审查公关也需要很多时间(和奉献)。在撰写本文时，我们已经回复了[3000 多个问题](https://github.com/issues?q=is%3Aopen+is%3Aissue+user%3Aspatie+is%3Apublic)并审查了[更多的公关](https://github.com/pulls?q=is%3Aopen+is%3Apr+user%3Aspatie+is%3Apublic)。

我们在 Spatie 做长期规划，但我们也有每周一次的短期规划会议。当安排下周的日程时，我们只计划四天。所以我们有一个工作日可以灵活安排。不要把那一天想象成一个固定的日子，那段时间大多分散在那一周。

在那“第五”天，我们公司的每个人都被允许从事开源工作或他们自己的项目(如果可能的话，也将是开源的)。对于我们的员工来说，这有一个很好的好处，他们可以通过在客户项目和开源项目之间切换来保持事情的趣味性。

就我个人而言，我也很喜欢在空闲时间开发包。所以每周有几次，我会在晚上花些时间继续改进已发布的包并创造新的东西。

## 迎接队伍

因为我经常写博客、发推特并尝试在会议上发言，我已经成为公司开源努力的公众形象。但你应该知道我不是一个人在做这些。我身后有一个伟大的团队，他们做了大量的工作。

从 Spatie 开始，我就是我们公司唯一的后端开发人员。2015 年年中，当 [Seb](https://twitter.com/sebdedeyne) 加入我们的团队时，情况发生了变化。他喜欢从事更低级的 PHP 和 JavaScript 工作。他是 [regex](https://github.com/spatie/regex) 、 [schema-org](https://github.com/spatie/schema-org) 、[phpunit-snapshot-assertions](https://github.com/spatie/phpunit-snapshot-assertions)和许多其他包的主要作者。而我们的 [dashboard](https://github.com/spatie/dashboard.spatie.be) 和 [JavaScript](https://spatie.be/en/opensource/javascript) 代码如果不是他也不会这么好。一定要把他的博客加入书签。

去年，我们有一个很棒的实习生，亚历克斯，他同时加入了我们的团队。他正在帮助维护[的拉腊维尔许可](https://github.com/spatie/laravel-permission)，这使得[相当受欢迎](https://packagist.org/packages/spatie/laravel-permission)。他目前正在为一些未发布的包/项目做一些非常棒的工作，我现在还不能谈论这些。

几个月前，我们雇佣了第一位远程员工 [Harish](https://twitter.com/introwit) 。他正在帮助解决我们所有回购的问题，并为[我们的 dns 包](https://github.com/spatie/dns)奠定了基础。

我们的新员工布伦特喜欢关注绩效。他的热情当然反映在他一直在做的开源工作上。他创建了 [laravel-binary-uuid](https://github.com/spatie/laravel-binary-uuid) 包，他(以一种友好的方式)敦促我在我们的媒体库中添加对响应图像的[支持。目前他正忙着创建一个非常酷的异步包。他还有一个很酷的博客。](https://github.com/spatie/laravel-medialibrary/issues/810)

最后但同样重要的是，还有 Spatie 的创始人 [Willem](https://twitter.com/willemvbockstal) 。他负责[的外观，我们的仪表板](https://github.com/spatie/dashboard.spatie.be)，我们的 [dns 查询服务](https://dnsrecords.io/)，我们软件包的各种[演示页面](http://vue-table-component.spatie.be/)，我们 Patreon 页面的副本[等等。](https://www.patreon.com/spatie)

我真的很高兴我们团队的每个成员都喜欢开源工作。希望在可预见的未来，他们都能继续为斯帕蒂工作。我真的希望，如果有人离开我们去另谋高就，他们会把开源精神带到他们的新事业中。

## 最受欢迎的 10 大套餐

考虑到下载量，以下是我们最受欢迎的 10 个软件包:

1.  [laravel-backup](https://github.com/spatie/laravel-backup)
2.  [拉腊维尔分形](https://github.com/spatie/laravel-fractal)
3.  [db-dumper](https://github.com/spatie/db-dumper)
4.  [laravel-permission](https://github.com/spatie/laravel-permission)
5.  [pdf 转图像](https://github.com/spatie/pdf-to-image)
6.  [laravel-媒体库](https://github.com/spatie/laravel-medialibrary)
7.  [分形](https://github.com/spatie/fractalistic)
8.  [laravel-时事通讯](https://github.com/spatie/laravel-newsletter)
9.  [laravel-glide](https://github.com/spatie/laravel-glide)
10.  [拉韦尔-尾巴](https://github.com/spatie/laravel-tail)

其中一些包，比如 [db-dumper](https://github.com/spatie/db-dumper) ，可能会获得很大的下载提升，因为其他包需要它们。

## 不那么受欢迎的套餐

有一些我们自己在每个项目中使用的包，我希望它们会更受欢迎。我希望通过在这里提到他们，他们会得到一点关注。如果你决定试试下面提到的这些，我希望你会像我们一样喜欢使用它们。

这一类的第一个是 [laravel-uptime-monitor](https://docs.spatie.be/laravel-uptime-monitor/v3/introduction) 。这个软件包可以检查多个网站的正常运行时间。它会在你的站点关闭时通知你(当它恢复运行时)。您也可以在某个站点上的 SSL 证书过期前几天收到通知。在引擎盖下，该包利用 Laravel 原生通知，所以很容易使用 Slack，Telegram 或您首选的通知提供商。

其次，还有[laravel-translated](https://github.com/spatie/laravel-translatable)。这个可以让你的口才模型装翻译。与 [Dimitris Savvopoulos 的优秀翻译包](https://github.com/dimsav/laravel-translatable)不同，我们的不需要单独的翻译表来存储翻译。我们的包将翻译作为 json 存储在表的一列中。它非常容易使用，不需要额外的查询来获取翻译。我们的想法是将翻译作为 json 存储在来自[的专栏中，Mohamed Said](https://github.com/themsaid) (他现在是 Laravel 的第一名员工？).

尽管你已经看到 laravel-medialibrary 进入了前 10 名，但我认为它应该更高一些。该软件包可以将所有类型的媒体文件(图像、pdf 等)与雄辩的模型相关联。它可以生成缩略图，可以在外部文件系统上存储文件，并生成文件的 URL。它很容易使用，并且有[优秀的文档](https://docs.spatie.be/laravel-medialibrary/v6/introduction)。我们的团队已经在努力创建 v7，它将增加对动态压缩资产、响应图像、客户端组件等等的支持。

## 你如何对我们的努力表示感谢

也许我以前应该提到它，但是我们的套餐不是完全免费的。它们上面有一个特殊的许可证:明信片。如果我们的任何产品进入您的生产环境，我们将非常感谢您给我们寄明信片。这是我们的地址:

斯帕蒂 bvba
T1】桑伯斯特拉特 69D
T3】2060 安特卫普
T5】比利时

每周邮递员给我们送来一些明信片。对我和我的团队来说，很高兴知道我们的产品正在全球范围内被使用。我们收到了来自各大洲的明信片。我们甚至收到了一张来自瓦努阿图的照片，对我们来说，它远在地球的另一边。你也可以看看我们的明信片系列。它们都是[在我们网站上发布的](https://spatie.be/en/opensource/postcards)。

另一种表达感激的方式是成为我们的一员。就像在我们的 Patreon 页面上提到的，您的捐赠将帮助我们投入更多的时间来维护和创建新的开源代码。

你也可以通过在 GitHub 上关注我们的[回复来鼓励我们。](https://github.com/spatie/)

## 未来

我们没有新软件包的固定路线图。这取决于我们正在处理的客户项目。如果有什么东西可以用通用的方法解决，我们将创建一个包。

有一段时间，我认为，因为我们已经解决了许多常见的问题，我们团队的包输出会减少一点。但事实恰恰相反。几乎我们团队的每个成员都有一些好的包装想法。

作为一个团队，我们现在正在做的一个更大的事情是前面提到的媒体库的新版本。你可以期待它在 2018 年 Q1 奥运会的某个地方落下。

## 在关闭

创建和使用开源包仍然是一种真正有趣的体验。在技术层面上，我学到了很多我在单独做客户项目时学不到的东西。就像你可能已经从字里行间体会到的那样，我的同事们享受并感激为我们的开源工作付出的时间，这也给我带来了巨大的快乐。

我希望你喜欢我们开源努力背后的故事。最重要的结论是，这个故事中只有赢家。如果你在你的公司处于倡导或积极致力于开源的位置，我强烈建议你这样做。

如果你以前没有用过我们的软件包，看看我们公司网站上的列表。或许我们做了一些对你有用的东西。