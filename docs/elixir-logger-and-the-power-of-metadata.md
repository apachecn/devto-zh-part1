# 药剂记录器和元数据的力量

> 原文：<https://dev.to/timber/elixir-logger-and-the-power-of-metadata>

*原载于[timber . io](https://timber.io/blog/elixir-logger-and-the-power-of-metadata)T3】*

在本帖中，我们将探讨通过添加上下文元数据，Elixir 的日志记录器可以生成非常有用的日志的方法。

[Logger](https://hexdocs.pm/logger/Logger.html) 是随 Elixir core 发布的 OTP 应用程序之一。这是一个强大而优雅的日志记录框架，能够承受生产环境的压力，并且对生成日志的过程几乎没有开销。它还具有不可思议的可扩展性，提供了在最终呈现之前操作和增强日志的方法。

当一个 Elixir 进程生成一个日志时，Logger 处理将日志数据异步传输到一个后端列表。后端负责写出数据。默认情况下，Elixir 项目将使用`:console`后端，它将日志消息打印到 STDOUT。

## 记录元数据

与许多语言附带的默认日志包不同，Logger 将日志元数据视为头等大事。在其最基本的用法中，元数据对于将关于相同主题的日志行分组在一起很有用。例如，您可以将 HTTP 请求的请求 ID 设置为元数据，所有关于该 HTTP 请求的日志行将显示该请求 ID。

记录器元数据是存储在流程字典中的关键字列表，因此每个流程都有一个独立的元数据存储。这意味着在一个进程中设置元数据不会影响另一个进程中的元数据。每当您记录消息时，关键字列表都会被复制，并与消息一起发送到每个日志记录器后端。后端可以选择如何处理元数据。在下面的例子中，我们将使用默认的`:console`后端，因为它很容易上手。

### 配置`:console`

`:console`后端使元数据的启动和运行变得容易。默认配置是在日志级别之前立即打印元数据。您只需要将您将在`config/config.exs` :
中使用的元数据键列入白名单

```
config :logger, :console,
  metadata: [:request_id] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，`:console`被配置为打印出`:request_id`元数据；任何其他元数据都将被忽略。在 Elixir 1.5 中，引入了特殊设置`:all`，让`:console`后端打印出所有元数据:

```
config :logger, :console,
  metadata: :all 
```

Enter fullscreen mode Exit fullscreen mode

元数据白名单是`:console`后端独有的。其他后端不会需要。

### 为以后添加元数据

有两种方法可以将元数据添加到日志中。首先是使用`Logger.metadata/1`函数。这个函数获取一个关键字列表，并将其合并到现有的元数据中。当您生成一段您知道以后会有用的数据时，这非常有用，就像上面描述的 HTTP 请求 ID。事实上， [`Plug.RequestId`](https://github.com/elixir-plug/plug/blob/v1.4.3/lib/plug/request_id.ex#L54) 模块在设置请求 ID:
时会这样做

```
defp set_request_id({conn, request_id}), header) do
  Logger.metadata(request_id: request_id)
  Conn.put_resp_header(conn, header, request_id)
end 
```

Enter fullscreen mode Exit fullscreen mode

当在上面的函数中调用 Wehn `Logger.metadata/1`时，同一个进程中的每个日志语句都将请求 ID 作为元数据的一部分！让我们假设我们正在处理一个请求 ID 为`bcc1fe`的 HTTP 请求，并且插件已经处理了元数据的设置。现在，我们在我们的业务逻辑中记录以下消息:

```
Logger.info("Updated profile for user ##{user_id}") 
```

Enter fullscreen mode Exit fullscreen mode

控制台上的最终输出将如下所示:

```
22:57:14.199 request_id=bcc1fe [info]  Updated profile for user #32 
```

Enter fullscreen mode Exit fullscreen mode

### 为单行添加元数据

第二种选择是在记录时添加元数据。使用此选项，元数据不会被持久化，因此它仅用于该日志行。当你有一些只与线路本身相关的东西时，比如支付确认码:
时，这真的很有用

```
defp handle_payment_success(p = %Payment{confirmation_code: c}) do
  Logger.info("Successfully processed payment", confirmation_code: c)
  p
end 
```

Enter fullscreen mode Exit fullscreen mode

这里，日志消息表明支付成功，并给出了作为元数据的确认码。您生成的日志行将类似于以下内容:

```
22:58:43.040 confirmation_code=9184210 [info]  Successfully processed payment 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您在上一步中设置了 HTTP 请求 ID:

```
22:58:43.040 confirmation_code=9184210 request_id=bcc1fe [info]  Successfully processed payment 
```

Enter fullscreen mode Exit fullscreen mode

它非常简单，但提供了一个非常强大的方法来增强您的日志。

## 实践中的元数据

元数据是增强日志的一种非常强大的方法，但是有效地使用它可能很困难。如果你添加了太多的元数据，你会开始产生太多你不注意的噪音，特别是当你有大量的日志时。不要忘记，除了您已经在生成的日志之外，您还有由您所依赖的第三方库生成的日志。他们将获取您设置的元数据，并可能添加他们自己的元数据。将所有这些日志合并成可用的信息需要一个游戏计划。

从思考你的数据开始。当完成一个请求时，哪些相关信息在所有日志行中总是相同的？这是您希望使用`Logger.metadata/1`提前设置为元数据的元数据类型。随着应用程序的进展，每个公共数据点就像一根将日志线连接在一起的绳子。

考虑我们在设置元数据时使用的初始示例。HTTP 请求 ID 在接收请求和响应请求时很有用，但对于中间的所有步骤也很有用。它将处理完成该请求的日志行链接在一起。

如果您处理的是一个以用户为中心的系统，您可能还会引入用户的 ID 作为元数据。这成为将日志行链接在一起的另一个点，而不是通过单个请求将它们链接在一起，而是随着时间的推移将它们链接在一起。同样，假设您开始一项任务，异步更新您的电子商务目录中产品的图像。您可以使用产品 ID 作为元数据来关联与该更新任务相关的所有日志行(以及与该产品 ID 相关的任何其他操作)。

然而，并不是所有的信息都是相关的。有些信息只与特定的步骤相关，这是您应该在生成 log 语句的同时设置的元数据。

支付确认码的例子就是这种情况。确认码只对付款处理步骤有用(在大多数情况下)，因此，只将其添加到相关的日志语句中是有意义的。同样，处理数据库查询所花费的时间也可以作为元数据添加。HTTP 响应的状态代码也是如此。这些值与它们各自的日志语句最相关，通过将它们设置为元数据，在浏览日志时可以很容易地看到它们。

跨多个日志行的数据的相关性取决于您的业务需求。您可能会发现，对于您的业务来说，将客户 ID 设置为关联日志行的元数据是合适的。另一家企业可能只记录特定操作的客户 ID，如结账。如果您开始思考为什么要查看日志，以及如何确定日志语句是相关的，那么不用做太多工作，数据点就变得很明显了。

尝试元数据是最简单的入门方式，而且涉及的工作非常少。随着时间的推移，您可以逐渐改进和优化元数据的使用，直到它满足您的需求。

在以后的文章中，我们将探讨 Timber 如何建立在元数据之上，以及为什么一旦元数据到达后端，Elixir Logger 的内置支持就能让您(和我们)做出惊人的事情。