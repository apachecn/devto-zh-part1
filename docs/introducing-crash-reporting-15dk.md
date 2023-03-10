# 介绍崩溃报告

> 原文：<https://dev.to/bugfenderapp/introducing-crash-reporting-15dk>

*这篇文章最初发表在我们的[博客](https://bugfender.com/blog/introducing-crash-reporting/)上。*

[![](img/ad4a245b40582a3e0681f497a39ef3cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dNe6jXn5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/01/BF-Crash-Reporting.jpg)

Bugfender 是一款工具，通过从使用其应用程序的设备中收集日志，来帮助移动开发者调试他们的应用程序，并提供更好的客户支持。本质上，Bugfender 就像一根很长的 USB 电缆，将所有运行你的应用程序的手机连接到你的电脑上…即使你的应用程序是从 App Store 或 Google Play 下载的，而你从未见过用户本人。得心应手，对吧？

TL；DR:除了能够捕获所有设备的日志，我们还增加了记录应用程序崩溃的可能性。请继续阅读，了解更多详情。

## 为什么崩溃？

一个应用程序可以生成许多日志，这些日志可能很难浏览。在创建 Bugfender 后不久，我们引入了标记发生异常情况的会话的功能，从而更容易确定哪些日志需要首先关注。例如，如果用户多次无法登录，或者服务器给出意外的响应，您可以标记会话，以指示它包含错误以及错误发生的确切时间，以便您可以在以后检查这些日志。这就是我们所说的[问题](https://bugfender.com/issues-integrations/)。

但是，有些问题我们可以自动检测，例如，当应用程序遇到非托管错误并关闭时。这通常被称为“崩溃”或“强制关闭”。Android 也有一个常见的崩溃，称为“应用程序不响应”(ANR)，当应用程序在没有更新用户界面的情况下花费太长时间来完成任务时就会发生这种情况。

我们有意识地决定不在 Bugfender 的第一个版本中添加崩溃报告，主要是因为它已经包含在其他工具中，如 Crashlytics 或 Firebase。我们决定 Bugfender 可以在这些产品没有覆盖的其他领域增加价值，并协同工作，例如收集日志或其他类型的异常的能力。

你可能会问，为什么现在要添加崩溃报告呢？嗯，经过近四年的运营，Bugfender 已经是一个成熟的工具，我们觉得是时候扩展了。崩溃收集一直是我们的客户最需要的功能之一；寻找一个中心位置来管理日志和崩溃。所以我们听了你的话。感谢您的反馈，这对我们继续将产品引向正确的方向非常重要。请继续加油！

## 碰撞收集在挡泥板中的优势

使用 Bugfender 进行崩溃收集的主要优势当然是将所有日志和报告放在一个位置。如果发生崩溃，您可以查看该会话中的日志，甚至可以在其他会话中浏览该设备的日志，而不必在工具之间切换。

另一个优势是 Bugfender 是一个付费工具。你说优势？嗯，当你不为产品付钱时，就意味着你就是产品。当你使用一个免费的工具时，你是通过放弃你或你的客户的数据来间接支付费用的。这种融资方式没有错，但并不是所有人都同意这种方式。一个人必须有意识地做出决定。

崩溃收集是所有付费计划的特色，但是在我们的测试期间，它也将提供给免费用户。

如何立即开始使用崩溃报告

首先，您需要将您的 SDK 更新到最新版本。然后，安装 Bugfender 时调用`enableCrashReporting()`就简单了。

在 Objective-C 中，写:
`[Bugfender enableCrashReporting];`

用 Swift、Java 和 Kotlin 写:
`Bugfender.enableCrashReporting()`

启用后，崩溃将出现在您的问题选项卡中，如下所示:

[![](img/ecc662514c95f6f7a4a4fe1142b0c939.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Cwa_VbY7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2018/01/crash-reporting.jpg)

## 当前的限制

目前，崩溃报告仅适用于 iOS 和 Android，我们还没有计划支持 macOS。路线图上的
是**生产**发布的崩溃象征。目前你会看到没有函数名和行号的崩溃信息，这可能是有用的，所以肯定有改进的空间，我们在未来可以扩展。

如果您遇到这种情况，您仍然可以手动符号化崩溃日志，但这需要一点工作。将来，我们将为您提供一种上传符号映射文件的方式。iOS 中的 dSYM 文件或 Android 中的 mapping.txt)以便我们可以直接在 Bugfender 仪表板中执行符号化。

## 这是否意味着我不能继续使用 Crashlytics 或 Firebase？

当然，你当然可以。这甚至可以让你两全其美，因为 Crashlytics/Firebase 可以给你一些有用的统计数据和象征意义(诚实第一)。相反，Bugfender 可以为您提供导致崩溃的所有日志和上下文。

## 现在开始收集崩溃

记录应用程序的崩溃将帮助您轻松地收集和识别用户的痛点。崩溃是应用程序商店一星评论的头号驱动因素，你应该不惜一切代价避免它们。

要开始利用崩溃报告，下载我们最新的 SDK 并遵循安装说明，包括调用 **`enableCrashReporting()`** 方法。[如果你还没有一个](https://app.bugfender.com)，你将需要一个免费账户。

如果您在使用此功能时遇到任何问题或有任何疑问，请通过支持聊天联系我们[。我们很乐意帮助您！](#)

*这篇文章最初发表在我们的[博客](https://bugfender.com/blog/introducing-crash-reporting/)上。*