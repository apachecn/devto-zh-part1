# 将无聊的电子邮件通知变成可行的轻松对话

> 原文：<https://dev.to/picsoung/turn-boring-email-notifications-into-actionable-slack-conversations-dd3>

昨天，我与一位客户讨论，回答了他们关于我们的 [3scale API 管理](http://3scale.net)解决方案的一些问题。除了其他事情之外，他们还对我们的*“警报系统”*以及我们如何在他们的 API 出现高流量的情况下通知他们感到好奇。简单！如果有人即将达到 API 配额，我们会向您发送电子邮件。

几乎在我回答这个问题的同时，我想我听到自己在说…“等等，什么？！电子邮件？!"。有点抱歉，我们没有提供更现代的解决方案。我们结束了通话，我怀着强烈的意愿四处查看，看看我们如何才能改善这种体验。我必须弄清楚！这是我早间黑的故事。

<figure>[![](img/091f42a941e11349e9f281388a76b934.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rgs6RwMr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Abc01avAKIn8kMyN7XZ6s4A.png) 

<figcaption>丑陋邮件通知</figcaption>

</figure>

<figure>[![](img/57fe07d6eb1797d5dd27ada9242604cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QHrF2yFm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/461/1%2A7pX-AQdOPIjLaUb0Dlamrw.jpeg) 

<figcaption>预期导致懈怠</figcaption>

</figure>

#### 连接积木🔌🏗

我不知道你，但当我想到通知时，我通常会想到 [Slack](http://slack.com) 。这就是我试图为我使用的工具收集所有自动报告的地方。这个黑客会向 Slack 发送通知。

此外，遗憾的是，我们的工程师没有时间来构建一个好的警报 API，所以我将不得不使用我拥有的资源:*一封电子邮件*。

有许多像 [Context.io](http://context.io) 或 [Nylas](http://nylas.com) 这样的邮件 API 可以让你获取电子邮件并解析它们，但这需要处理很多开销(OAuth、过滤电子邮件等等)。最好的解决方案是将通知发送到指定的电子邮件地址，并在那里解析它们。

Zapier 通过他们的[解析器](http://parser.zapier.com)产品提供了这一特性。他们给你一个电子邮件地址，你可以发送所有的电子邮件进行分析。它包括一个解析 UI，您可以在其中定义想要提取的变量。

最后，为了在消息格式上有更多的灵活性，我们不会直接将 Zapier 插入 Slack，而是使用 Zapier webhook 操作将提取的数据发送到托管在 [Glitch](http://glitch.com) 上的应用程序。

我们正在构建一个完整的无服务器解决方案😇

<figure>[![](img/0d079ab39388722d538fe4a71ad50856.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qEwGjW6I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AEIYMhfIow0kHz_gmfOJyng.png) 

<figcaption>Webhook 上的整合形式</figcaption>

</figure>

#### 设置时差🤖💬

为了能够向您的 Slack 团队发送消息，我们需要创建一个**传入 Webhook** 集成。按照这个[链接](https://my.slack.com/services/new/incoming-webhook/)，它应该会引导你配置集成。在那里，您将定义在哪个通道上发送通知。完成后，将 Webhook URL 放在手边，我们稍后会用到。

<figure>[![](img/b2a775b1a1a4ec1d85238b0284dea803.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a-BM3-p5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZ7gQW0QMmxTHXulWtxRI0g.png) 

<figcaption>扎皮尔解析器 UI</figcaption>

</figure>

#### 设置 Zapier 解析器和触发器📥⚙️

在 Zapier 上，我们将从配置邮件解析器[开始，这里是](http://parser.zapier.com)。一旦你有一个地址，发送一个通知电子邮件的例子，这样我们就可以设置一个模板。几秒钟后，邮件应该被解析器收到，你应该看到它出现了。然后，突出显示您想要从电子邮件中提取的变量，并给它们命名。

一旦模板被定义，我们将配置一个 *Zap* 来连接电子邮件解析器和我们的应用程序。在 *Zap* 中，选择**邮件解析器** app 触发**新邮件**动作。如果您测试这一步，您应该能够检索您先前发送到*解析器*电子邮件地址的消息。

#### app🎏💻

该应用程序将公开一个 **POST** route */hook* ，其中 Zapier 将发送一个包含提取数据的请求。你可以在 Glitch 上检查[我的例子](https://glitch.com/edit/#!/email-to-slack-notification)，并用你自己的变量重新混合它。

在*中。env* 文件，您应该将值添加到变量中。获取我们之前在 Slack 上创建的 Hook URL，并通过添加用户名和图标为您的通知添加一些个性。 *ZAPIER_SECRET* 变量应该是一个随机字符串，我们将使用它来识别请求来自 Zapier webhook。

消息格式化发生在 *slack.js* 文件上。您可以从 Slack 中检查[消息生成器](https://api.slack.com/docs/messages/builder)来运行格式化测试。

<figure>[![](img/ded33e44180efc1d9c46f43db172b098.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YL34922l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AA5w8rg_IrlmAwKMWXlM5wg.png) 

<figcaption>在扎皮尔</figcaption>

</figure>

上配置 Webhook

#### 给 Zapier 添加 webhook 动作🎣⚙️

现在，我们的应用程序出现了故障，我们可以向我们的 *Zap* 添加一个动作。

添加一个指向 URL [的 POST 类型的 Webhook 动作 https://YOUR _ GLITCH _ APP _ name . GLITCH . me/hook](https://YOUR_GLITCH_APP_NAME.glitch.me/hook)

In 应该返回一个 JSON 有效负载。然后定义将发送到应用程序的变量，并将它们映射到我们提取的变量。

<figure>[![](img/7911d7ecfcf93b8f5a5a8932eadf1a03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LtvPqRnF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWwPma046NW1DY1kwvfX-Rw.png) 

<figcaption>安全保密报头</figcaption>

</figure>

最后，为了安全起见，我们将定义一个名为 *X-zapier-secret* 的头，其值与我们之前定义的 **ZAPIER_SECRET** 环境变量的值相同。

#### 测试并享受🎉🎊

就这样，你现在完成了👍

测试流，看看消息是否按照您想要的方式呈现。当你对结果满意时，打开*开关*。

<figure>[![](img/393dcdf8ac6f1829a872e7073469400a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VzbdCada--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/452/1%2AVryUy0b0Gxr57rmceDHp1A.png) 

<figcaption>预期结果松弛</figcaption>

</figure>

现在，您可以忽略电子邮件通知，因为它们都以一种美丽而全面的方式发送到 Slack。

如果你想增强这个流程，我们可以稍后添加[可操作按钮](https://api.slack.com/docs/message-buttons)，但这需要更多的工作。

让我知道你对这些黑客的看法！