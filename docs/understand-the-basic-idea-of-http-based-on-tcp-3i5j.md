# 了解基于 TCP 的 HTTP 的基本思想

> 原文：<https://dev.to/chooyan/understand-the-basic-idea-of-http-based-on-tcp-3i5j>

HTTP 是现在使用最多的技术，也是所有 web 服务不可或缺的。

我们可以用自己喜欢的 HTTP 客户端库轻松发送 HTTP 请求，也可以用自己喜欢的任何语言和框架轻松运行 HTTP 服务器。我们不需要考虑库或框架中正在发生什么。

然而，我相信理解 HTTP 的基本思想对许多程序员是有用的，例如，当考虑应该添加什么 HTTP 头或者考虑什么是 HTTP/2 时。

在这篇文章中，我将解释 HTTP 是如何工作的，从运行 TCP 套接字程序开始。

# 微小的 TCP 套接字程序

让我们先来看看 TCP，它是 HTTP 的底层，有一个很小的 TCP 套接字程序。

TCP 的能力很简单，以字节流的形式发送/接收数据。没有数据格式，没有数据结尾，没有发送数据的时间规则。因此，客户端可以随时向服务器发送任何数据，反之亦然。

这是 TCP 服务器/客户端的 Ruby 程序示例，两者都可以发送数据，从键盘输入，并输出从对方接收的数据。

```
# server.rb

require 'socket'

port = 20000

server = TCPServer.new(port)
socket = server.accept

Thread.new do
  loop do
    data = socket.gets
    p data
  end 
end 

loop do
  data = gets
  socket.print(data)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
# client.rb

require 'socket'

host = '127.0.0.1'
port = '20000'

socket = TCPSocket.open(host, port) 

Thread.new do
  loop do
    data = socket.gets
    p data
  end
end

loop do
  data = gets
  socket.sendmsg data
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过打开两个终端并运行上面的每个脚本来检查它们是如何工作的。

```
$ ruby server.rb 
```

Enter fullscreen mode Exit fullscreen mode

```
$ ruby client.rb 
```

Enter fullscreen mode Exit fullscreen mode

*请注意，`server.rb`必须首先运行，因为`client.rb`期望服务器已经运行。

你可以打任何你想要的字母。当您键入 Enter 键时，您可能会注意到客户端和服务器可以随时相互发送/接收数据。

# 使用 HTTP 进行通信

现在，我们来看看 HTTP 客户端如何发送 HTTP 请求，使用前面的`client.rb`程序。

在继续之前，我们需要一个简单的 HTTP 服务器作为沙箱。

理论上，我们可以通过扩展上面的`server.rb`程序来构建 HTTP 服务器程序，但是在这篇文章中这么做太有挑战性了。

不用自己搭建 HTTP 服务器程序，Ruby 中的`WEBrick::HTTPServer`在 localhost 中作为 HTTP 服务器可用。只需复制&下面粘贴的 CLI 命令，并运行它来启动一个 HTTP 服务器。

```
$ ruby -rwebrick -e 'WEBrick::HTTPServer.new(:DocumentRoot => "./", :Port => 20000).start' 
```

Enter fullscreen mode Exit fullscreen mode

本节的目标是将 HTTP 请求发送到上面的沙盒服务器，并通过使用`client.rb`程序发送适当的数据来接收 200 个具有良好响应正文的响应。

## HTTP 请求的格式

正如我们在上一节中看到的，使用 TCP 套接字的客户机和服务器之间的通信没有任何关于发送/接收数据的规则。

基于 TCP 的 HTTP 是提供“服务器和客户端如何相互通信”的规范的协议之一。换句话说，HTTP 定义了数据格式、发送数据的时间、何时关闭连接等。

您可以在 [RFC 7230](https://tools.ietf.org/html/rfc7230) 上查看详细规格。(我也在努力阅读这一页来写这篇文章)

让我们从第一行请求开始。如[节-3.1.1:RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.1.1) 所定义，HTTP 方法、请求目标和 HTTP 协议版本出现在第一行，格式如下。

> 方法 SP 请求-目标 SP HTTP-版本 CRLF

如果我们想用`HTTP/1.1`向根路径发送`GET`请求，比如第一行应该是这样的。

```
GET / HTTP/1.1 
```

Enter fullscreen mode Exit fullscreen mode

所以我们试着用`client.rb`发送上面的数据。

运行`client.rb`并输入上面的字符串。

```
$ ruby client.rb
GET / HTTP/1.1 
```

Enter fullscreen mode Exit fullscreen mode

然后，按回车键**两次**，您将得到响应代码为 200 的 HTTP 响应。

```
"HTTP/1.1 200 OK \r\n"
"Content-Type: text/ html; charset=\"UTF-8\"\r\n"
"Server: WEBrick/1.3.1 (Ruby/2.4.1/2017-03-22)\r\n"
"Date: Tue, 23 Jan 2018 13:33:51 GMT\r\n"
"Content-Length: 9173\r\n"
"Connection: Keep-Alive\r\n" 
```

Enter fullscreen mode Exit fullscreen mode

酷！我们可以用简单、小巧、玩具式的 TCP 套接字程序成功地向服务器发送 HTTP 请求。

## 接收 HTTP 响应

现在，仔细看看服务器何时向客户机发送响应。

请记住，当您发送 HTTP 请求时，您按了两次 enter 键**，即使第一行(" GET / HTTP/1.1 ")是在按了一次 enter 键之后发送的。**

 **这是因为服务器应该在客户端发送完所有数据后返回响应。因此，服务器必须根据 HTTP 协议的规范，通过查看数据本身来评估它已经接收到了全部数据。

整个 HTTP 请求格式是这样定义的。(参见[第 3 节:RFC 7230](https://tools.ietf.org/html/rfc7230#section-3) )

> HTTP-message = start-line
> *(头字段 CRLF )
> CRLF
> 【消息体】

您可能会注意到,`header-field`应该跟在第一行之后，所以服务器无法在明确表示`header-field`结束之前做出响应。换句话说，服务器用接收双`CRLF`来计算`header-field`的结束。(参见`CRLF`首先出现在`header-field`的末尾，然后在下一个空行再次出现)

这就是为什么 HTTP 服务器在按了两次回车键后才给客户端发送响应，也就是说`header-field`为空，请求消息到此结束。

除了 HTTP/1.1 中引入的`Keep-Alive`头，HTTP 连接会在服务器向客户端发送一次响应后关闭。[(附录 A.1.2:RFC 7230)](https://tools.ietf.org/html/rfc7230#appendix-A.1.2)

> 在 HTTP/1.0 中，每个连接都是由客户端在请求前建立的，并在发送响应后由服务器关闭。

您可以在收到响应后再次尝试发送数据，您可能很快会注意到连接已经关闭。(`socket.sendmsg`重复返回`nil`

HTTP 连接已经结束，您必须再次连接到服务器。

# 总结

在这篇文章中，我们体验了用 TCP 套接字程序连接到 HTTP 服务器。现在很清楚，HTTP 基于 TCP 连接，并引入了如何使用 TCP 在 web 应用程序和客户端(移动应用程序、CLI 程序等)之间进行高效通信的通用规则**。**

请注意，这篇文章中介绍的关于 HTTP 协议的信息**只是 HTTP 协议的几个特征**。要在实践中理解 HTTP，您需要知道更多，比如请求头的角色、连接管理(比如在`Keep-Alive`头中)，或者 RFC 文档中陈述的其他内容。

希望这篇文章能帮助你理解 HTTP 如何工作的第一步。**