# 爆破运动——帮助高级运动员的高级记录

> 原文：<https://dev.to/bugfenderapp/blast-motion--advanced-logging-to-help-advanced-athletes>

[![](img/b3639f1276960f2546889c263aecb839.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JnbUhKbq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2017/07/bugfender-blastmotion.jpg)

在我们的[博客](https://bugfender.com/blog/blast-motion-advanced-logging-to-help-advanced-athletes/)上阅读这篇文章。

Blast Motion 是那种酷到让你忍不住吹嘘自己是你的客户的公司。我的意思是，这些人制造传感器和应用程序来分析运动员的摆动、击打、跳跃和运动。他们是美国职业棒球大联盟的官方球棒传感器技术！

他们使用 [Bugfender](https://bugfender.com/) 来登录他们的 iOS 和 Android 应用。

我与 Blast Motion 应用程序的系统架构师 Steve Wehba 聊了聊他们如何在开发、测试和生产过程中使用日志记录，以及 Bugfender 如何帮助他们。

## 大量设备的大量日志记录

首先，设置一些背景很重要。Blast Motion 制造小型传感器，可以安装在运动器材(如棒球棒或高尔夫球杆)甚至运动员身上。这些传感器从高度校准的传感器收集大量数据，然后通过(通常)蓝牙低能量(BLE)将数据发送到连接的手机或平板电脑，以进行进一步处理和显示。来自传感器的数据和移动应用程序中完成的处理相结合，使运动员能够深入了解他们的表现。你可以在 https://blastmotion.com/products/baseball/的看一个关于棒球如何运作的短片。

如果你像我一样，这个设置给你两个想法。1)这听起来是一个非常有趣的产品。2)有很多不同的组件，每一个都有自己特殊的方式导致难以调试的问题。史蒂夫证实，让一切在艰难的情况下可靠地工作——比如在一个挤满智能手机用户的体育场使用 been 需要做大量的工作。

[![](img/5d3bd0beff883d0488ab09dc9cbd5da9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qDPql-4M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2017/07/blast-955_large.jpg)

爆炸运动传感器及其应用

为了帮助解决这个问题，Blast Motion 创建了一个跨平台的代码框架，在他们的移动应用程序之间共享。该框架处理常见的事情，如 BLE 连接和一些低级运动分析，以及日志记录。对于日志记录，它们捕获开发人员显式记录的典型额外数据，如方法名和行号。它们还捕捉爆炸运动的特定数据，例如关于用户(可以是运动员或教练)的信息。

他们在正确的日志级别上添加了大量的日志记录，如果情况需要，允许他们只捕获正常生产使用或详细调试的错误(在运行时配置，而不推送新的构建)。还要记住，传感器没有独立的网络连接，必须依靠移动应用程序来执行记录。

爆炸传感器实际上非常有趣(除了内置大量高度校准的传感器的固有兴趣之外)。它可以在没有移动设备的情况下运行一段时间，捕捉和存储数据，然后在连接到移动设备时将数据下载到移动设备上。这方面的一个用例是滑冰或滑雪板，运动员仅使用传感器进行跑步，然后连接到移动设备进行分析。Blast 传感器还可以使用特定于活动的模式来仅捕获感兴趣的事件周围的数据，例如当它检测到蝙蝠击球时前后的几秒钟。

这一切都意味着，Blast Motion 必须对它们捕捉的日志记录、收集和转发数据的时间和方式进行规范和组织。既要得到他们需要的东西，又要尊重他们的用户喜欢的带宽和存储限制。

[![](img/f837bfa9fb94f36b1ce37f0652af72a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eUtPykl4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2017/07/bugfender-blastmotion-2.jpg)

## 客户支持日志–不仅仅是开发人员

Blast Motion 使用 Bugfender 的一个引人注目的方面是，他们的客户支持也可以访问日志并定期审查它们。客户支持还指导客户在情况需要时在应用程序中启用额外的日志记录。这种对日志的访问有助于支持人员更快地解决更多问题，如果需要升级到开发阶段，还可以提供更完整的信息以简化调试。

这种安排之所以成为可能，是因为 Bugfender 提供了专注于移动且易于使用的界面。在使用 Bugfender 之前，Blast Motion 将他们的移动应用程序日志放入他们的集中式日志记录系统，该系统还处理他们的后端日志。该系统要复杂得多，必须从后端处理大量日志。额外的复杂性使得用它来支持客户变得不可行(并且让开发人员和 QA 恼火)。但是一旦他们切换到 Bugfender 的移动应用程序日志，开放访问支持突然变得简单了。

## 按开发阶段拆分日志

Steve 与我分享的最有价值的见解之一，以及他们使更广泛地访问日志变得可行的方法之一，是他们如何管理不同开发阶段的日志，并使查找相关设备的日志变得更容易。

对于用户可用的每个 iOS 或 Android 应用程序，他们实际上在 Bugfender 中创建了 3 个版本的应用程序:开发、测试和生产各一个。这种安排使得用户可以根据他们的角色轻松查看与他们相关的日志。开发人员可以添加他们在开发过程中需要的混乱、详细的调试日志，而不会在支持人员可访问的生产日志中造成混乱和误报。类似地，QA 可以专注于当前测试中的应用。

他们在如何命名开发和测试设备方面也非常自律，这使得每个人都可以快速轻松地找到他们正在测试或开发的设备的日志。

我喜欢这些解决方案，因为它们非常简单。没有查询或配置。只需导航到与您正在进行的工作相对应的应用程序，并找到您需要查看的设备。

[![](img/d165ff3947f9247cfeca96bdf2f9aaa7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xkea6Tnv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2017/07/bugfender-blastmotion-cloud-sync.jpg)

## 调试云同步

史蒂夫还分享了 Bugfender 帮助他们追踪的一个特别难的 bug。他们有一个自定义的云同步组件(在移动端和后端都有)，允许他们在多个设备之间共享运动表现分析(想象一下，一名运动员在手机上捕捉数据，但稍后在 iPad 上进行详细检查，或访问教练)。在某个时候，他们开始遇到数据不同步的问题。

就像所有可怕的 bug 一样，它时断时续，难以重现。他们查看了 Bugfender 日志，变得更加困惑:

*   应用程序正在正确启动与服务器的同步
*   正在连接服务器(所以这不是一个基本的网络问题)
*   但是服务器返回 400 HTTP 错误代码——表明这不是服务器问题(这将是一个 500 错误),而是客户端问题

下一步是标准程序:他们在调试器下运行应用程序，并在应用程序试图向服务器发送数据的地方设置断点，以便他们可以单步执行代码。当然，一切正常。它们在没有调试器的情况下运行，可能会产生错误，但绝不会在调试器下运行。

此时，他们将日志记录提升到最高级别，并设法用完整的日志来捕获故障。当他们仔细研究详细的客户端和服务器日志时，他们开始注意到日志的时间戳有一个非常小的偏差——客户端总是领先一点点。

这是理解 bug 的关键。原来，服务器为了保护自己免受配置错误或行为不当的客户端的攻击，会拒绝任何带有时间戳的数据。因此，当移动设备的时钟比服务器快时，发送的数据可能带有时间戳，从服务器的角度来看，这是无效的，因为它是在未来。时钟偏差足够小，当应用程序在调试器中的断点处暂停时，服务器将有足够的时间在应用程序恢复后接收到的数据显示为有效。

一旦他们理解了问题并提醒自己分布式时钟非常困难，修复就很容易了(分布式时钟带来的挑战是谷歌 Spanner 分布式数据库[依赖于每个节点上的原子钟和 GPS](https://www.wired.com/2012/11/google-spanner-time/)的一个原因！).

对我来说，这是为什么日志如此有价值的一个完美的例子:它允许你在与正常运行完全相同的条件下测试你的软件，同时仍然获得你理解问题所需要的数据。调试器很棒，但它们必然会改变软件的运行方式。在这种情况下，时钟是罪魁祸首，这种差异使得错误无法重现。在其他对时间敏感的代码或排序很重要的代码(线程或异步代码可能就是这种情况)中，您会看到同样的问题。

[![](img/a0dc259d820ca400ba1628734419a99b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h3ow2nk---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2017/07/blast-motion-baseball-sensor2.jpg)

## 最后的想法

Blast Motion 是在完整的生产环境中使用 Bugfender 和一组大型复杂应用程序的一个很好的例子。听到 Bugfender 的移动聚焦功能集如何使其成为他们工具箱中的正确工具，对我们来说是鼓舞人心的。我想感谢史蒂夫与我们分享他的故事和见解。与一个成功找到解决难题的实用方案的天才开发人员交谈总是很棒的。

### 关于爆炸运动

总部位于加利福尼亚州卡尔斯巴德的 Blast Motion 正在定义可穿戴动作捕捉技术的未来。通过将行业最完整的性能改进解决方案和实时指标分析与自动策划的视频精彩片段相结合，Blast Motion 创造了丰富的用户体验，增强了人们捕捉、分析和改进游戏的方式。有关爆破运动的更多信息，请访问:[blastmotion.com](https://blastmotion.com/)。

在我们的[博客](https://bugfender.com/blog/blast-motion-advanced-logging-to-help-advanced-athletes/)上阅读这篇文章。