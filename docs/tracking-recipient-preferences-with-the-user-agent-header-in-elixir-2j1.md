# 使用 Elixir 中的用户代理标题跟踪收件人首选项

> 原文：<https://dev.to/sparkpost/tracking-recipient-preferences-with-the-user-agent-header-in-elixir-2j1>

### 使用 Elixir 中的用户代理标题跟踪收件人首选项

*注意:这篇用户代理标题文章使用了用 Elixir 编写的代码来说明自己。如果你愿意，你可以[阅读 PHP 版本](https://www.sparkpost.com/blog/preferences-user-agent-header-php/)。*

人们对特定人群的相对商业价值已经做了很多研究。从超级消费者到影响者， [iPhone 用户](https://www.theguardian.com/technology/2016/nov/02/mobile-web-browsing-desktop-smartphones-tablets)到桌面顽固分子，了解你的接收者的偏好[显然是重要的](http://uk.businessinsider.com/gap-android-ios-conversion-rates-revenue-per-session-shrinks-iphone-moovweb-2015-6?r=US&IR=T)。在这个高度个性化的时代，多了解一点你的客户群也是件好事。幸运的是，这是一个非常简单的 SparkPost 消息事件的工作。

在这篇文章中，我将回顾一下`User-Agent`头的内容，然后浏览从 SparkPost 的 webhooks 工具接收跟踪事件的过程，解析收件人的用户代理头，并使用结果构建一个简单但可扩展的报告来跟踪操作系统偏好。在本文中，我将使用 Elixir 作为示例代码，但是大多数概念都可以移植到其他语言。

### SparkPost Webhook 参与事件

SparkPost webhooks 为您的应用程序提供了一种低延迟的方式来接收您的电子邮件流量的详细跟踪事件。我们之前已经写了关于[如何使用它们](https://www.sparkpost.com/blog/webhooks-beyond-the-basics/)和[如何构建它们](https://www.sparkpost.com/blog/webhooks-infrastructure/)，所以如果你需要的话，你可以阅读一些背景材料。

这里我们将只关注`click`事件。每当收件人点击你邮件中被跟踪的链接时，SparkPost 就会生成一个`click`事件，你可以通过 webhook 接收到这个事件。你可以直接从 SparkPost API [这里](https://api.sparkpost.com/api/v1/webhooks/events/samples?events=click)获取一个点击事件的例子。对于我们的目的来说，最有趣的字段自然是`msys.track_event.user_agent`,它包含当收件人点击链接时，他们的电子邮件客户端发送的完整的用户代理头。

```
{ "msys": { "track_event": { // ... "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_3) AppleWebKit/537.36 
(KHTML, like Gecko) Chrome/41.0.2272.118 Safari/537.36" // ... } } } 
```

Enter fullscreen mode Exit fullscreen mode

### 搜索用户代理

好的，我们几乎可以从这一小块文字中挑出重要的细节。对于专业人士来说，有一个规范，但是很难读懂。概括地说，我们可以从用户代理字符串中提取有关用户的浏览器、操作系统和设备的详细信息。

比如来自我自己的用户代理:

```
Mozilla/5.0 (Linux; Android 7.1.1; Nexus 6P Build/N4F26O) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.91 Mobile Safari/537.36 
```

Enter fullscreen mode Exit fullscreen mode

…你可以看出我是一个使用华为 Nexus 6P 设备的 Android 用户(而且它是最新的；).

**警告:用户代理欺骗**

你们中的一些人可能会担心我们的用户代理与我们使用的服务共享的信息。作为你的权利，你可以使用一个浏览器插件( [Chrome](https://chrome.google.com/webstore/detail/user-agent-switcher-for-c/djflhoibgkdhkhhcedjiklpkjnoahfmg) 、 [Firefox](https://addons.mozilla.org/en-gb/firefox/addon/user-agent-switcher/) )或者内置的浏览器开发工具来改变你的用户代理字符串。网络上的一些服务会根据你的用户代理改变你的体验，所以了解这些工具对你的影响是很重要的。

### 从 SparkPost 跟踪事件中获取用户代理

好了，理论到此为止。让我们构建一个小的 webhook 服务来接收、处理和存储通过我们的 SparkPost 帐户跟踪的每次点击的用户代理详细信息。

### 仙丹与凤凰网

在 Elixir 中构建 web 服务的事实上的标准方法是 [Phoenix Framework](http://phoenixframework.org/) 。如果你对 Phoenix 入门指南感兴趣，那么[的文档非常好](https://hexdocs.pm/phoenix/overview.html)，尤其是[的启动和运行指南](https://hexdocs.pm/phoenix/up_and_running.html)是一个很好的起点。

我们将假设您已经有了一个基本的 Phoenix 应用程序，并专注于添加一个 HTTP 端点来接受 SparkPost webhook 事件批次。

**插件:用于网络的可组合模块**

Elixir 附带了一个名为“Plug”的规范(这里定义了[)使得在 HTTP 服务上构建所谓的中间件层变得很容易。最简单的 plug 形式是接受一个连接和一组选项的函数。我们将使用这个表单来建立我们的 webhook 消费者。](https://github.com/elixir-plug/plug)

### 处理 SparkPost Webhooks 请求

我们的第一个任务是创建一个“管道”，它是一个连接所经历的一系列转换。Phoenix 中的管道只是一种组合一系列插件并将它们应用于某组传入请求的便捷方式。

我们将首先创建一个“webhook 管道”,然后添加插件来处理我们服务中的各种任务。所有这些都发生在我们应用程序的路由器模块:

```
# Create a new plug pipeline to handle SparkPost webhooks requests pipeline :webhook do
# Use the accepts plug plug :accepts, ["json"] end 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 Phoenix 文档的[布线部分阅读更多关于 Phoenix 布线和 plug pipelines 的内容。现在，重要的是要认识到每个 Phoenix 应用程序都包括一个端点模块，它负责设置基本的请求处理。这包括自动 JSON 解析，我们在这里将依赖它。](https://hexdocs.pm/phoenix/routing.html)

### 解包 SparkPost 事件

我们的事件结构包含一定量的嵌套，我们现在可以去掉这些嵌套，为使用其中的美味细节做准备。这是我们第一个插件的工作:

```
# Define a new plug to extract nested event fields def 
unpack_events(conn, _) do first_key_fn = &hd(Map.keys(&1))   
cleanevents = conn.params["_json"] |> Enum.map(fn evt -> 
evt["msys"] end) |> Enum.map(fn evt -> evt[first_key_fn.(evt)] 
end) assign(conn, :events, cleanevents) end # Add the new plug 
to our pipeline pipeline :webhook do plug 
:accepts, ["json"] plug :unpack_events end 
```

Enter fullscreen mode Exit fullscreen mode

这里有一点神奇。在管道开始之前，我们的端点将 JSON 解析器插件应用于所有请求。然后，我们的`unpack_events`插件可以依赖连接 JSON 解析器上的`_json`参数。

其余的`unpack_events`只是提取每个事件上的`msys`键的内容和该对象中第一个键的内容。最后，我们的`unpack_events`插件将我们解包的事件存储在一个连接参数中，供以后的插件获取。

### 过滤事件

现在让我们只保留点击事件(当我们稍后向 SparkPost 注册我们的 webhook 时，我们也可以要求它只发送点击事件):

```
def filter_event_types(conn, types) do good_event? = 
&Enum.member?(types, &1["type"]) assign(conn, :events, 
Enum.filter(conn.assigns[:events], good_event?)) end # Apply event 
filtering after unpacking pipeline :webhook do plug :accepts, 
["json"] plug :unpack_events plug :filter_event_types ['click'] end 
```

Enter fullscreen mode Exit fullscreen mode

这个插件将我们过滤的事件留在`:events`连接参数上。`filter_event_types`接受我们关心的类型列表。

单个事件中有很多细节。把事情精简到我们关心的领域可能是个好主意:

```
def filter_event_fields(conn, kv) do pick_fields = &Map.take(&1, 
kv) assign(conn, :events, Enum.map(conn.assigns[:events], 
pick_fields)) end # JSON -> unpack -> filter on clicks -> extract 
user_agent fields pipeline :webhook do plug :accepts, ["json"]   
plug :unpack_events plug :filter_event_types, ['click'] plug 
:filter_event_fields, ['user_agent'] end 
```

Enter fullscreen mode Exit fullscreen mode

**插管线后:控制器**

为了完成我们的 webhook 请求处理，我们需要一个*控制器*，它在插件管道之后工作，处理请求并为客户端生成响应。这里有一个骨架控制器:

```
defmodule TrackUserAgentsWeb.ApiController do use 
TrackUserAgentsWeb, :controller def webhook(conn, _params) do     
json conn, %{"ok": true} end end 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以将`ApiController.webhook/2`连接到我们的路由器:

```
scope "/webhook", TrackUserAgentsWeb do pipe_through :webhook     
post "/", ApiController, :webhook end 
```

Enter fullscreen mode Exit fullscreen mode

当我们向 SparkPost 注册我们的 web 服务作为 webhook 消费者时，它将向它发出包含 JSON 事件有效负载的 HTTP 请求。现在我们的服务有了一个`/webhook`端点，它接受 JSON，缩减我们的事件批次，并以一个愉快的“ok！”。

### 测试我们的进度

我们可以通过向服务发送一个测试批次来测试我们的服务。幸运的是，SparkPost API 会根据您的请求[生成一个测试批次](https://developers.sparkpost.com/api/webhooks.html#webhooks-events-samples-get)。

1.  从 SparkPost API 中抓取一个 webhooks 事件批次样本:注意:这个步骤使用了 [cURL](https://curl.haxx.se/) 和 [jq](https://stedolan.github.io/jq/) 。您可以跳过 jq 部分，自己从 JSON 文件中删除结果键。
2.  `curl https://api.sparkpost.com/api/v1/webhooks/events/samples | jq .results > batch.json`
3.  开始我们的凤凰服务:
4.  `mix phx.server`
5.  将我们的测试批次发送给服务部门:
6.  `curl -XPOST -H "Content-type: application/json" -d @batch.json http://localhost:4000/webhook`

### 解析用户代理

现在我们准备用新的信息来丰富我们的活动。我们将解析用户代理字符串，并使用 [ua_inspector](https://hex.pm/packages/ua_inspector) 模块提取操作系统。我们可以很容易地将这个步骤添加到路由器的 API 插件管道中:

注意:如果您正在跟进，记得在 mix.exs 中添加`ua_inspector`作为依赖项，并对其进行配置。

```
def os_name(ua) do if not Map.has_key?(ua, :os) do "unknown" 
else case ua.os do %UAInspector.Result.OS{} -> ua.os.name 
_ -> "unknown" end end end def parse_user_agents(conn, _) do 
events = conn.assigns[:events] |> Enum.map(&Map.put(&1, 
"user_agent", UAInspector.parse(&1["user_agent"]))) |> 
Enum.map(&Map.put(&1, "os", os_name(&1["ua"]))) assign(conn, 
:events, events) end # JSON -> unpack -> filter on clicks -> extract 
user_agent -> filter nulls -> parse pipeline :webhook do plug 
:accepts, ["json"] plug :unpack_events plug :filter_event_types, 
['click'] plug :filter_event_fields, ['user_agent'] plug 
:parse_user_agents end 
```

Enter fullscreen mode Exit fullscreen mode

注意:并不是所有的用户代理字符串都包含我们想要的细节(或者根本没有意义)，所以我们用“OS: unknown”来标记所有奇怪的点击。

好了，现在我们有了一个事件数组，它只包含感兴趣的字段，并有一个额外的“os”字段来引导。

### 生成报表就绪汇总数据

在这一点上，我们可以只列出每个事件，并呼吁我们的报告完成。然而，我们已经开始期望在我们的报告中有一些总结，以简化理解的任务。我们对电子邮件收件人的操作系统趋势感兴趣，这表明我们应该汇总我们的结果:收集按操作系统索引的摘要。也许我们甚至会使用谷歌图表饼状图。

我们可以在这里引用“给读者的练习，但我总是发现这令人沮丧，所以,[这里有一个包含电池的实现](https://github.com/SparkPost/elixir-webhook-sample),它在 PostgreSQL 中存储点击事件摘要，并使用 Google Charts 呈现一个简单的报告。

### 给读者的一个练习

我知道，我说过我不会这么做的。请原谅我:如果您关注上面的实现步骤，您可能会注意到几个可重用的元素。具体来说，我提取了一些过滤和报告参数供重用:

*   事件类型过滤器
*   事件字段过滤器
*   事件”丰富功能

只需很少的努力，您就可以添加、过滤和分组`campaign_id`字段，以查看按电子邮件活动细分的操作系统偏好。您还可以使用它作为基础，从带有`type=bounce, fields=rcpt_to,bounce_class`的反弹事件中更新您自己的用户数据库，等等。

我希望这个简短的演示能给你一些关于使用 SparkPost webhooks 的实用见解。通过一点试验，这个项目可以适合大量的用例，我非常乐意接受关于这个主题的贡献。如果你想谈论更多关于用户代理头、你自己的事件处理需求、SparkPost webhooks、Elixir 或其他任何东西，来找我们吧！

在酏剂中使用用户代理标题跟踪收件人偏好的帖子[最先出现在](https://www.sparkpost.com/blog/user-agent-header-elixir/) [SparkPost](https://www.sparkpost.com) 上。