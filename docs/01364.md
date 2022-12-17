# 在 Elixir 中实现对等网络——第 1 部分:服务器

> 原文：<https://dev.to/cerqueira/implementing-a-peer-to-peer-network-in-elixir---part-1-the-server-59p6>

*这篇文章最初发表在 [opencode.space 博客](https://opencode.space/implementing-a-peer-to-peer-network-in-elixir-part-1)上。*

在过去的一周左右，我一直在使用 Elixir 实现一个点对点网络，这是我正在进行的一个项目的一部分。我想写这个帖子系列，因为我还没有在网上找到很多解决这种情况的资源，其次是记录整个过程供将来参考。

即使您不想实现对等网络，这仍然是学习更多知识和试验 OTP 应用程序和概念的一个很好的练习。

我们将实现一个对等网络，允许对等体向其他对等体发送文本消息，并接收相同的消息。很简单，对吧？

该系列分为三部分:

1.  在 Elixir 中实现对等网络-第 1 部分:服务器
2.  ~~在 Elixir 中实现对等网络-第 2 部分:客户端~~
3.  ~~在 Elixir 中实现对等网络——第 3 部分:增强功能~~

在这篇文章中，我们将讨论对等网络服务器端的逻辑。最终，您将拥有一个工作的 TCP 服务器，它侦听和接受连接，并回显它收到的每条消息。

完成后，您就可以使用 Telnet 进行连接和测试了。

## 创建项目

使用 [Mix](https://hexdocs.pm/mix/Mix.html) 创建一个带有`--sup`标志的新项目，以生成一个包含监督树和`application`回调设置的 OTP 应用程序框架。为了简单起见，我将这个项目命名为`network` :

```
$ mix new network --sup 
```

Enter fullscreen mode Exit fullscreen mode

## 设置依赖关系

对于这个项目，我们需要的唯一依赖项是 Ranch <sup id="fnref1">[1](#fn1)</sup> 。更新 **mix.exs** 以包含它:

```
defp deps do
  [
    {:ranch, "~> 1.4"}
  ]
end 
```

Enter fullscreen mode Exit fullscreen mode

完成后，获取依赖项:

```
$ mix deps.get 
```

Enter fullscreen mode Exit fullscreen mode

## 监听连接

为了让某人连接到我们的服务器，我们必须倾听并接受他们的到来。这就是牧场 <sup id="fnref1">[1](#fn1)</sup> 库派上用场的地方。

创建**lib/network/server . ex**:

```
defmodule Network.Server do
  @moduledoc """
  A simple TCP server.
  """

  use GenServer

  alias Network.Handler

  require Logger

  @doc """
  Starts the server.
  """
  def start_link(args) do
    GenServer.start_link(__MODULE__, args, name: __MODULE__)
  end

  @doc """
  Initiates the listener (pool of acceptors).
  """
  def init(port: port) do
    opts = [{:port, port}]

    {:ok, pid} = :ranch.start_listener(:network, :ranch_tcp, opts, Handler, [])

    Logger.info(fn ->
      "Listening for connections on port #{port}"
    end)

    {:ok, pid}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在`init/1`函数上，神奇的事情发生了。我们使用`:ranch.start_listener/5`函数创建一个接受者进程池，它将接受传入的连接，当它接受时，产生一个新的进程用指定的协议(`Network.Handler`)来处理它。

`:ranch.start_listener/5`需要的五个参数是:

1.  `:network` -标识监听器的唯一名称
2.  `:ranch_tcp` -交通工具
3.  `[{:port, port}]` -运输选项
4.  `Network.Handler` -协议处理器
5.  `[]` -处理者的选项

## 处理连接

因为 Ranch <sup id="fnref1">[1](#fn1)</sup> 让我们将协议处理抽象到它自己的模块中——这非常有用，因为它最小化了代码复杂性——这就是我们现在要做的。

创建**lib/network/handler . ex**:

```
defmodule Network.Handler do
  @moduledoc """
  A simple TCP protocol handler that echoes all messages received.
  """

  use GenServer

  require Logger

  # Client

  @doc """
  Starts the handler with `:proc_lib.spawn_link/3`.
  """
  def start_link(ref, socket, transport, _opts) do
    pid = :proc_lib.spawn_link(__MODULE__, :init, [ref, socket, transport])
    {:ok, pid}
  end

  @doc """
  Initiates the handler, acknowledging the connection was accepted.
  Finally it makes the existing process into a `:gen_server` process and
  enters the `:gen_server` receive loop with `:gen_server.enter_loop/3`.
  """
  def init(ref, socket, transport) do
    peername = stringify_peername(socket)

    Logger.info(fn ->
      "Peer #{peername} connecting"
    end)

    :ok = :ranch.accept_ack(ref)
    :ok = transport.setopts(socket, [{:active, true}])

    :gen_server.enter_loop(__MODULE__, [], %{
      socket: socket,
      transport: transport,
      peername: peername
    })
  end

  # Server callbacks

  def handle_info(
        {:tcp, _, message},
        %{socket: socket, transport: transport, peername: peername} = state
      ) do
    Logger.info(fn ->
      "Received new message from peer #{peername}: #{inspect(message)}. Echoing it back"
    end)

    # Sends the message back
    transport.send(socket, message)

    {:noreply, state}
  end

  def handle_info({:tcp_closed, _}, %{peername: peername} = state) do
    Logger.info(fn ->
      "Peer #{peername} disconnected"
    end)

    {:stop, :normal, state}
  end

  def handle_info({:tcp_error, _, reason}, %{peername: peername} = state) do
    Logger.info(fn ->
      "Error with peer #{peername}: #{inspect(reason)}"
    end)

    {:stop, :normal, state}
  end

  # Helpers

  defp stringify_peername(socket) do
    {:ok, {addr, port}} = :inet.peername(socket)

    address =
      addr
      |> :inet_parse.ntoa()
      |> to_string()

    "#{address}:#{port}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个模块有一些非常有趣的特殊性。首先，你可能已经注意到，由于定义了函数和回调，我们已经实现了`GenServer`行为，尽管我们没有使用`GenServer.start_link/3`函数，而是使用了`:proc_lib.spawn_link/3`。

在进入更多细节之前，让我们看看`init/3`函数。乍一看都很清楚:我们确认与`:ranch.accept_ack/1`的连接，将连接设置为活动，然后...我们进入一个循环？

当然可以！我们需要在一个循环中等待来自连接的新消息，并且在接收到一个消息时，我们做它需要的任何处理，进入循环并再次等待新消息。

当我们实现`GenServer`行为时，我们必须使用`:gen_server.enter_loop/3`,这将我们的流程变成了`:gen_server`流程，并进入`:gen_server`流程接收循环。

现在回去，为什么`:proc_lib.spawn_link/3`？如果你意识到了`GenServer`的行为，你就会知道你必须定义一个`start/3`或`start_link/3`函数来启动服务器，一旦它启动了，它就会调用`init`回调。到目前为止一切顺利。

这个问题的发生是因为这种行为的运作方式。根据 [`GenServer.start_link/3`文档](https://hexdocs.pm/elixir/GenServer.html#start_link/3):

> 为了确保同步启动程序，该功能在`c:init/1`返回之前不会返回。

当我们需要进入一个循环时，这将引发一个大问题，因为当你进入循环时，它将永远不会返回，直到发生了不好的事情并返回一个错误。这就是我们为什么使用`:proc.spawn_link/3`的原因，因为它将异步产生流程，而不是同步产生流程，我们不会有任何问题。

实际上，唯一可以使用`:gen_server.enter_loop/3`的进程是那些用这个特殊函数启动的进程。

`handle_info/2`回调将接收每个 TCP 事件。这些可以是:

*   `{:tcp, socket, message}` -客户端发送的正常消息
*   `{:tcp_error, socket, reason}` -连接出现的任何错误
*   `{:tcp_closed, socket}` -当连接关闭时

我们还定义了一个`stringify_peername/1`助手函数，为给定的连接提供一个友好的名称。它使用`:inet.peername/1`函数来检索连接
的地址和端口，并返回一个包含两个值的字符串。

## 启动服务器

更新 **config/config.exs** 以包含服务器配置:

```
use Mix.Config

config :network, :server,
  port: String.to_integer(System.get_env("PORT") || "5555") 
```

Enter fullscreen mode Exit fullscreen mode

更新**lib/network/application . ex**，将`Network.Server`包含在应用的监督树中:

```
defmodule Network.Application do
  @moduledoc false

  use Application

  def start(_type, _args) do
    # Get configuration
    config = Application.get_env(:network, :server)

    children = [
      # Add it to supervison tree
      {Network.Server, config}
    ]

    opts = [strategy: :one_for_one, name: Network.Supervisor]
    Supervisor.start_link(children, opts)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

打开终端并启动应用:

```
$ mix run --no-halt
00:00:00.000 [info] Accepting connections on port 5555 
```

Enter fullscreen mode Exit fullscreen mode

打开另一个终端并使用 Telnet 连接:

```
$ telnet 127.0.0.1 5555
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^['. 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们已经成功连接到服务器。在我们的应用程序运行的终端上，我们还应该看到一条消息通知我们:

```
00:00:00.000 [info] Peer 127.0.0.1:00000 connecting 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试通过 telnet 会话发送任何消息，它都会被回显。在应用程序的终端上，您会看到(例如):

```
00:00:00.000 [info] Received new message from 127.0.0.1:00000: "Hello, opencode.space!\r\n". Echoing it back 
```

Enter fullscreen mode Exit fullscreen mode

如果您关闭运行 Telnet 的终端，我们的应用程序也会得到通知:

```
00:00:00.000 [info] Peer 127.0.0.1:00000 disconnected 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

仅仅在`~153 LOC`年，我们就成功地实现了一个 TCP 服务器，它可以回应收到的每一条消息。很整洁，不是吗？

在本系列的下一部分，我们将介绍如何实现一个连接到服务器的客户机，以实现一个对等网络。

保持关注更新！

## 备注

* * *

1.  [Ranch](https://github.com/ninenines/ranch) 是由[nine nine](https://ninenines.eu/)开发的 TCP 协议套接字接受池。欲了解更多信息，请访问库的[用户指南](https://ninenines.eu/docs/en/ranch/1.4/guide)或[功能参考](https://ninenines.eu/docs/en/ranch/1.4/manual) [↩](#fnref1)