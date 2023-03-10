# 每个开发人员都应该编写一个个人自动化 API

> 原文：<https://dev.to/anotherdevblog/every-developer-should-write-a-personal-automation-api>

## IFTTT，个人自动化的基石

[![IFTTT Logo](img/a36c197607e4b4cdc1f112fa37d939e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VVvNVYaH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/ifttt-logo.png)

几年前我爱上了[If This Then than](https://ifttt.com/)(If TTT)。这是一项出色的免费服务，让你可以为数百个应用、网络服务、IOT 设备等建立简单的规则。这是一个伟大的概念，如此惊人，以至于我无法在这篇文章中公正地解释它。如果你不熟悉 IFTTT(或者微软托管的新竞争对手， [Flow](https://flow.microsoft.com/en-us/) ，放下手头的工作，马上去看看。说真的，我可以等。

IFTTT 填补了我以前从未想过要填补的数字生活中的一个空白。但是在使用了一段时间后，我发现自己渴望更高层次的定制。我想转换输出，将规则链接在一起，并添加条件语句。基本上，我想让 IFTTT 成为一个免费的、托管的 cron 作业执行器，拥有漂亮的用户界面、移动应用程序和数百个我可以使用的库。我喜欢他们提供的基本规则引擎的简单性，但是我想要更多。我怀疑许多开发人员也有同样的感觉。

[![Maker Logo](img/73b9cbee4aa3dc6f739cf22879242d12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QXrc0VEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/maker-logo.jpg)

幸运的是，IFTTT 听到了 devs 的声音，增加了[创客频道](https://ifttt.com/maker)，开启了无限可能。可以把它看作是 IFTTT 本身的一个非常简单、标准化的 API 端点——一个围绕 IFTTT 支持的所有应用程序/服务的 HTTP API shell。你知道吗，你总是希望围绕你使用的服务有一些简单的 API，不需要大量的设置、OAuth、SDK 和文档？不客气。

在能够接收请求和能够调用任意 HTTP 端点之间，Maker channel 也为您提供了构建任何您能想到的自动化所需的一切。需要一些灵感吗？下面是我用我的 API 做的一些事情。

## 为自己的 API 出谋划策

### 位置报告

[![Location image](img/1aae4d22640e8a7aa9bd32bbf4413d28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--auB4t4T4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/location-map.jpg)

我在我的手机和我妻子的手机上安装了 [Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm) ，并设置了一个规则，这样每当我们的手机连接到 Wifi 网络或从 Wifi 网络断开时，以及当我们没有连接到 Wifi 网络时，每隔几分钟，我们的手机都会调用我的 API，并报告其位置和连接的网络。

在 API 端，我将每个人分为在家或不在家。从那里你可以追踪历史，看到最近的位置，等等。这本身就是一个简洁的替代物，可以替代 Life360 或 Google+ location widgets 等功能更全的第三方应用。对紧急情况(或不在场证明)有帮助，但主要是对下一篇文章至关重要。

### 智能家居自动化

[![Smart home image](img/7c461d92d5d6ba10cf4382f6cd877137.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4KdOfp8Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/smart-home.jpg)

我们有一个智能报警系统和恒温器，都与 IFTTT 兼容。当我的 API 检测到所有电话都报告为“离开”时，它会自动启动我们的警报系统，并将恒温器调到离开模式。我们中的任何一个人把车开进车道的那一刻，我们的手机就会连接到 Wifi，并向 API 报告我们到家了。在我们进入房子之前，API 解除了系统并将恒温器设置为家庭模式。除了基于位置的家庭/外出自动化之外，我还编写了一个 cron 作业来在白天和夜晚模式之间切换闹钟和恒温器。

我发现我们报警系统中的玻璃破碎传感器超级敏感。我让它听到自动售冰机的声音就爆炸。或者盘子叮当作响。或者我打喷嚏的时候。因此，我还在我的 Pebble 手表上安装了一个简单的 HTTP 请求应用程序，允许我通过按下手腕上的按钮来解除警报。

如果警报响起，并且在一分钟内没有静音，就会向住在附近的精通技术的亲戚发送一条短信，让他们知道如果他们没有收到我们的电话，可能会有问题。我在手表上设置了一个 SOS 功能，做同样的事情。

### 建立监控

[![Siren image](img/0014cdd3dd4d065105664c393d448434.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XbYmHdL5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/siren-light.jpg)

大多数构建监控系统都有某种构建后挂钩，可以用来很好地附加到 API 上。但即使没有，几乎所有人都会在每次构建时发送电子邮件。这就是我目前工作的现状。我将所有的邮件过滤到我工作的 Gmail 的一个特定文件夹中，并让一个脚本每分钟检查一次那个文件夹中是否有新的邮件。它查看消息并对其进行解析，以确定构建的当前状态以及我是否参与了构建。如果它坏了，而我对此有所贡献，它就会点击我的 API，通过[按钮](https://www.pushbullet.com/)向我的手机和手表发送一条威胁消息。

### 站点正常运行时间监控

[![404 image](img/a8595a480b79beeeeac1fd66b3eeeaea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YWyCcpTT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/404-page.jpg)

最近，学校里一个对家庭作业至关重要的网络资源开始经历一系列令人讨厌的服务器问题。很多。因此，我添加了一个 API cron 作业来查询 [UptimeRobot](https://uptimerobot.com/) 该资源的状态，并在该站点关闭或重新启动时发布到类 Slack 通道。这样，我们就可以在等待网站恢复的同时开始做其他事情，而不是一遍又一遍地点击刷新。

### 自动化库存管理

[![stocks image](img/8f44899338abb98c8be0d0d3a9a78852.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--unAHIxyh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/stocks.jpg)

是的，IFTTT 有一个非常基本的用于监控个股的集成。但是你得到的数据深度是相当可悲的。因此，我在我的 API 中添加了一些功能来维护我正在观察的股票符号列表，并每隔几分钟从实际上没有记录的雅虎金融 API 中查询这些符号的更丰富的数据。当股票进入 52 周高点/低点的某个高/低点时，我会收到通知，以防我想买入或卖出。

额外提示:对于真正大胆的人来说，我听说你可以用你的[罗宾汉](https://robinhood.com/)账户和它的[秘密 API](https://github.com/sanko/Robinhood) 来自动化整个事情。如果你能正确地实现自动化，自由交易会让交易近乎完美。但是如果你要经历这些麻烦，只要注册 Quantopian 并以正确的方式去做。

### 通用数据查询

[![DB image](img/2c27937901f54440c872a9e9356c45cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5GeZu3WW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/db.png)

真的，只要你愿意，任何东西都可以成为 API。我给你举个例子。在我和妻子让步买了第二辆车之前，我每天都坐公交车从学校去上班。但是取决于我在哪里，时间是什么，我有不同的选择哪条路线在什么时间到达什么站点。每天试图拼凑 4 个不同的路线安排简直是一场噩梦。

所以我把所有我关心的路线和站点的时间表信息输入到谷歌电子表格中。然后，我在 API 中添加了查询下一辆到达的公交车/位置的功能，并在我的智能手表中添加了该动作的触发器。走出教室，按下一个按钮，我会收到一个通知，让我确切地知道哪辆公交车将到达下一站，以及它们将在什么时候把我送到我的工作场所。简单却非常有用。

### 远程关机/重启电脑

[![Remote computer control image](img/780a6b2bdadbac379e467e06741bbc66.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QLS2FTYq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/laptop-remote.jpg)

如果我的工作机器超过几个小时没有使用，它将停止响应 RDP 连接。不知道为什么。我沿着“检查你的电源设置”路线和 BIOS 路线走下去，什么也没有。甚至查看了坞站固件可能存在的网络问题。

幸运的是，在这种状态下，机器仍然接收和响应所有其他网络流量。但是 TeamViewer、LogMeIn 和 Chrome Remote Desktop 都是大忌，所以我写了一个快速的 Windows 任务——你猜对了——每隔 5 分钟查询我的 API，询问它是否需要重启。这是一个非常临时的解决方法(我真诚地希望如此)，但是到目前为止它已经足够有用(并且经常被使用),以至于这个特殊的动作已经在我手机的主屏幕上赢得了一个 DO 按钮窗口小部件的位置。

## 编写自己的 API

那么，你如何编写你的 API 呢？您将使用什么堆栈？托管呢？如果你已经拥有一个运行你所选择的堆栈的个人网站，恭喜你。但如果没有，我有一个好的，强大的，免费的替代建议:

如果你不想经历设置自己的服务器的麻烦，并且对 JavaScript 很熟悉，那么看看作为 API 选项的 Google Apps 脚本。您编写的脚本可以配置为按计划执行您选择的功能，并且可以像服务器一样处理 HTTP 请求、调用其他 API，并在很大程度上运行您想要的任何代码。此外，它们带有内置的库，让你可以与你账户的谷歌日历、Gmail、驱动器、文档和基本上任何其他东西进行交互。

另一方面，如果您想要一个实际的、合适的服务器环境，但仍然不想支付任何费用，请查看许多提供微小但免费的 Linux 虚拟机的提供商。 [Cloud 9](https://c9.io/) 大概是那个竞技场里我最喜欢的。如果你加载额外的点。NET 内核，用漂亮的 C## [编写 API，因为你可以](http://www.hanselman.com/blog/PublishingAnASPNETCoreWebsiteToACheapLinuxVMHost.aspx) :) Azure、AWS 和其他也是不错的选择。

我最终选择了 Google Apps 脚本路线。我以前在编写 GmailSnooze 的时候使用过 Google Apps 脚本，所以我对它已经相当适应了，甚至在我的个人账户上，我甚至还没有达到他们的速度限制，也没有任何可靠性问题。至今无怨无悔。如果你想尝试一下 Google Apps 脚本，我会在下一篇文章的[中写下我是如何设置我的个人 API 的。敬请关注。](https://dev.to/personal-automation-api/part-1-getting-started/)

## 值得吗？

当我从事这个个人 API 项目时，我经常想到兰道尔·门罗著名的自动化工作图:

[![Automation Graph](img/49c0a0744951fff4b57b195573abd729.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M31EjT1p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anotherdevblog.net/asseimg/xkcd-automation-time-graph.png)

我经常问自己，“相比最初的任务，我真的节省了多少时间？我是不是正在掉进一个自动化兔子洞？”我知道在某些方面，自动化很方便，但不会改变生活。有些人花了太多的时间调试和重写才得到正确的结果。但是当我在我的 API 中添加每个新特性时，下一个特性就变得更容易实现了。

最重要的是，我的思维方式已经改变了。每当我脑海中闪现一个以“伙计，我希望我不必……”开始的想法时，它通常会以“结束”...我能以某种方式实现自动化吗？”实际上，这就是软件开发的全部。我们整天都在修补、自动化、微调和改进我们业务的各个方面。我们多久花几分钟看看我们可以做些什么来应用到我们的生活中？谁知道呢，你可能会找到一种方法来自动化正确的事情，它最终会成为下一件大事。

啊哦，我的手表响了。必须修复构建:)

* * *

*这篇文章最初出现在[另一个开发者博客](http://anotherdevblog.net/personal-automation-api/every-developer-should-write-a-personal-automation-api/)T3 上*