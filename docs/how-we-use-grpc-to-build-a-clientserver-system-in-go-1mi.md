# 如何在 Go 中使用 gRPC 构建客户机/服务器系统

> 原文：<https://dev.to/chilladx/how-we-use-grpc-to-build-a-clientserver-system-in-go-1mi>

*此文最初发布在[媒体](https://medium.com/pantomath/how-we-use-grpc-to-build-a-client-server-system-in-go-dd20045fa1c2)T3 上*

# 我们如何在 Go 中使用 gRPC 构建一个客户机/服务器系统

[![](img/61fa76795ec5ace99119ca1b9b1c1002.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---ZHjJJmQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AQIjD685SfIfFV-xXtdIofg.jpeg) 
*摄影:伊戈尔[奥夫谢尼科夫](https://unsplash.com/photos/6uWHZ74S5Rk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

这篇文章是关于我们如何使用 gRPC(T1)和 proto buf(T3)来构建一个健壮的客户端/服务器系统的技术演示。

我不会详细解释为什么我们选择 gRPC 作为我们的客户端和服务器之间的主要通信协议，许多伟大的帖子已经涉及了这个主题(如[这个](https://grpc.io/blog/vendastagrpc)、[这个](https://stackoverflow.com/questions/43682366/how-is-grpc-different-from-rest)或[这个](https://thenewstack.io/grpc-lean-mean-communication-protocol-microservices/))。
但简单来说:我们正在使用 [Go](https://golang.org/) 构建一个客户机-服务器系统，它需要快速、可靠、可伸缩(因此我们选择了 gRPC)。我们希望客户机和服务器之间的通信尽可能小，尽可能安全，并且完全符合两端的要求(因此我们选择了 [Protobuf](https://developers.google.com/protocol-buffers/) )。

我们还想在服务器端公开另一个接口，以防我们添加一个没有兼容 gRPC 实现的客户机:一个传统的 REST 接口。我们希望(几乎)免费。

## 上下文

在 Go 中，我们将开始构建一个非常简单的客户端/服务器系统，它将只交换虚拟消息。一旦两者相互交流并相互理解，我们将添加额外的特性，比如 TLS 支持、认证和 REST API。

本文的其余部分假设您理解基本的 Go 编程。它还假设您已经安装了`protobuf`包，并且`protoc`命令可用(同样，许多文章都涉及到这个主题，并且有[官方文档](https://developers.google.com/protocol-buffers/docs/proto3))。

你还需要安装 Go 依赖项，比如 protobuf 的 [go 实现，以及](http://github.com/golang/protobuf) [grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway) 。

这篇文章中显示的所有代码都可以在 https://gitlab.com/pantomath-io/demo-grpc 的[获得。因此，请随意获取存储库，并使用标签在其中导航。存储库应该放在您的`$GOPATH`的`src`文件夹中:](https://gitlab.com/pantomath-io/demo-grpc)

```
$ go get -v -d gitlab.com/pantomath-io/demo-grpc
$ cd $GOPATH/src/gitlab.com/pantomath-io/demo-grpc 
```

Enter fullscreen mode Exit fullscreen mode

## 定义协议

*git 标签:*[init-proto buf-definition](https://gitlab.com/pantomath-io/demo-grpc/tree/init-protobuf-definiton)

[![](img/87f72580a189accd96b2d508bc280da4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AM1Yl0mR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AN8g6AM5x4n64RCXYUjwtYQ.jpeg) 
*照片由[马克·拉斯姆森](https://unsplash.com/photos/yri82tuk2TQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍于 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

首先，您需要定义协议，即定义客户端和服务器之间可以说什么，以及如何说。这就是 Protobuf 发挥作用的地方。它允许您定义两件事情:服务和消息。一个`service`是服务器可以根据客户端的请求执行的动作的集合，一个`message`是这个请求的内容。简单来说，你可以说`service`定义了**动作、**，而`message`定义了**对象**。

在`api/api.proto`中写入以下内容:

```
syntax = "proto3";
package api;

message PingMessage {
  string greeting = 1;
}

service Ping {
  rpc SayHello(PingMessage) returns (PingMessage) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，您定义了两件事:一个名为`Ping`的`service`，它公开了一个名为`SayHello`的函数，带有一个传入的`PingMessage`，并返回一个`PingMessage`；和一个称为`PingMessage`的`message`，它包含在称为`greeting`的单个字段中，该字段是一个`string`。
您还指定使用`proto3`语法，而不是`proto2`(参见[文档](https://developers.google.com/protocol-buffers/docs/proto3))。

这个文件不能这样使用:它需要被编译。编译 proto 文件意味着为您选择的语言生成代码，您的应用程序将实际调用这些代码。
在一个 shell 中，`cd`到你的项目的根目录，并运行下面的命令:

```
$ protoc -I api/ \
    -I${GOPATH}/src \
    --go_out=plugins=grpc:api \
    api/api.proto 
```

Enter fullscreen mode Exit fullscreen mode

该命令生成文件`api/api.pb.go`，这是一个 Go 源文件，它实现了您的应用程序将使用的 gRPC 代码。您可以查看它，但是您不应该更改它(因为它将在您每次运行`protoc`时被覆盖)。

您还需要定义由`service Ping`调用的函数，因此创建一个名为`api/handler.go` :
的文件

```
package api

import (
  "log"

  "golang.org/x/net/context"
)

// Server represents the gRPC server
type Server struct {
}

// SayHello generates response to a Ping request
func (s *Server) SayHello(ctx context.Context, in *PingMessage) (*PingMessage, error) {
  log.Printf("Receive message %s", in.Greeting)
  return &PingMessage{Greeting: "bar"}, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `Server` `struct`只是服务器的一个抽象。它允许“将一些资源附加到您的服务器上，使它们在 RPC 调用期间可用；
*   `SayHello`函数是 Protobuf 文件中定义的函数，作为`rpc`对`Ping`T3 的调用。如果不定义它，就无法创建 gRPC 服务器；
*   `SayHello`以一个`PingMessage`为参数，返回一个`PingMessage`。`PingMessage` `struct`是在`api.proto`定义自动生成的`api.pb.go`文件中定义的。该函数还有一个`Context`参数(参见官方[博客文章](https://blog.golang.org/context)中的进一步介绍)。稍后你会看到你可以用`Context`做什么。另一方面，它也返回一个`error`，以防不好的事情发生。

## 打造最简单的服务器

*git 标签:* [初始化服务器](https://gitlab.com/pantomath-io/demo-grpc/tags/init-server)

[![](img/d617b0ac303b89daacd0674bc4bd8b7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UmKyV2lC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AGy5Z3hBbB3r1GUkB75D_eg.jpeg) 
*图片由[内森·杜劳](https://unsplash.com/photos/3CS0Ne4wBPw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

现在您已经有了一个协议，您可以创建一个简单的服务器来实现`service`并理解`message`。用你最喜欢的编辑器创建文件`server/main.go` :

```
package main

import (
  "fmt"
  "log"
  "net"

  "gitlab.com/pantomath-io/demo-grpc/api"
  "google.golang.org/grpc"
)

// main start a gRPC server and waits for connection
func main() {
  // create a listener on TCP port 7777
  lis, err := net.Listen("tcp", fmt.Sprintf(":%d", 7777))
  if err != nil {
    log.Fatalf("failed to listen: %v", err)
  }

  // create a server instance
  s := api.Server{}

  // create a gRPC server object
  grpcServer := grpc.NewServer()

  // attach the Ping service to the server
  api.RegisterPingServer(grpcServer, &s)

  // start the server
  if err := grpcServer.Serve(lis); err != nil {
    log.Fatalf("failed to serve: %s", err)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我分解代码，使它更清楚:

*   注意，您导入了`api`包，因此 Protobuf `service`处理程序和`Server` `struct`是可用的；
*   `main`函数首先在您想要绑定 gRPC 服务器的端口上创建一个 TCP 监听器；
*   接下来的事情非常简单:创建一个`Server`的实例，创建一个 gRPC 服务器的实例，注册`service`，然后启动 gRPC 服务器。

您可以编译您的代码来获得服务器二进制:

```
$ go build -i -v -o bin/server gitlab.com/pantomath-io/demo-grpc/server 
```

Enter fullscreen mode Exit fullscreen mode

## 创建最简单的客户端

*git 标签:* [init-client](https://gitlab.com/pantomath-io/demo-grpc/tree/init-client)

[![](img/aa8ba4b079fe9ff852894ea6ac351dce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HjiG2ESw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ac6G6qANKUoyZn3awfRii1g.jpeg) 
*图片由 [Clem Onojeghuo](https://unsplash.com/photos/RLJnH4Mt9A0?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

客户端也导入`api`包，这样`message`和`service`就可用了。所以创建文件`client/main.go` :

```
package main

import (
  "log"

  "gitlab.com/pantomath-io/demo-grpc/api"
  "golang.org/x/net/context"
  "google.golang.org/grpc"
)

func main() {
  var conn *grpc.ClientConn

  conn, err := grpc.Dial(":7777", grpc.WithInsecure())
  if err != nil {
    log.Fatalf("did not connect: %s", err)
  }
  defer conn.Close()

  c := api.NewPingClient(conn)

  response, err := c.SayHello(context.Background(), &api.PingMessage{Greeting: "foo"})
  if err != nil {
    log.Fatalf("Error when calling SayHello: %s", err)
  }
  log.Printf("Response from server: %s", response.Greeting)
} 
```

Enter fullscreen mode Exit fullscreen mode

再一次，分解是非常直接的:

*   `main`函数在服务器绑定的 TCP 端口上实例化一个客户端连接；
*   注意函数返回时正确关闭连接的`defer`调用；
*   `c`变量是`Ping`服务的客户端，它调用`SayHello`函数，并传递一个`PingMessage`给它。

您可以编译您的代码来获得客户端二进制:

```
$ go build -i -v -o bin/client gitlab.com/pantomath-io/demo-grpc/client 
```

Enter fullscreen mode Exit fullscreen mode

## 让他们说话

您刚刚构建了一个客户端和一个服务器，所以在两个终端中测试它们:

```
$ bin/server
2006/01/02 15:04:05 Receive message foo 
```

Enter fullscreen mode Exit fullscreen mode

```
$ bin/client
2006/01/02 15:04:05 Response from server: bar 
```

Enter fullscreen mode Exit fullscreen mode

## 轻松生活的工具

*git 标签:* [init-makefile](https://gitlab.com/pantomath-io/demo-grpc/blob/init-makefile/Makefile)

现在 API、客户端和服务器都工作了，你可能更喜欢用 Makefile 来编译代码、清理文件夹、管理依赖项等等。

所以在项目文件夹的根目录下创建这个`Makefile`。解释这个文件超出了本文的范围，它主要使用您之前已经生成的编译命令。

要使用`Makefile`，请尝试调用以下命令:

```
$ make help api                            Auto-generate grpc go sources
build_client                   Build the binary file for client
build_server                   Build the binary file for server
clean                          Remove previous builds
dep                            Get the dependencies
help Display this help screen 
```

Enter fullscreen mode Exit fullscreen mode

## 保障通信安全

*git 标签:* [add-ssl](https://gitlab.com/pantomath-io/demo-grpc/tree/add-ssl)

[![](img/7036390ff9f75e56fcfa74751426ded3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1u5Pv9LB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ak_WFayrFR6ciCOLn8fWI7g.jpeg) 
*照片由[纳撒尼尔·泰特赫](https://unsplash.com/photos/-4AFJ5yujcE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

客户机和服务器通过[HTTP/2](https://http2.github.io/)(gRPC 上的传输层)相互通信。消息是二进制数据(多亏了 Protobuf)，但是通信是明文。幸运的是，gRPC 集成了 SSL/TLS，可以用来认证服务器(从客户端的角度来看)，以及加密消息交换。

您不需要对协议做任何修改:它保持不变。这些更改发生在 gRPC 对象创建过程中，客户端和服务器端都是如此。注意，如果只改变一边，连接就不起作用了。

在修改代码之前，您需要创建一个[自签名 SSL 证书](https://en.wikipedia.org/wiki/Self-signed_certificate)。这篇文章的目的不是解释如何做到这一点，但[官方 OpenSSL 文档](https://www.openssl.org/docs/manmaster/man1/) ( `genrsa`、`req`、`x509`)可以回答你关于它的问题(DigitalOcean 也有关于它的[漂亮而完整的教程](https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs))。同时，您可以只使用`cert`文件夹中提供的文件。以下命令用于生成文件:

```
$ openssl genrsa -out cert/server.key 2048
$ openssl req -new -x509 -sha256 -key cert/server.key -out cert/server.crt -days 3650
$ openssl req -new -sha256 -key cert/server.key -out cert/server.csr
$ openssl x509 -req -sha256 -in cert/server.csr -signkey cert/server.key -out cert/server.crt -days 3650 
```

Enter fullscreen mode Exit fullscreen mode

您可以继续并更新服务器定义以使用证书和密钥:

```
package main

import (
  "fmt"
  "log"
  "net"

  "gitlab.com/pantomath-io/demo-grpc/api"
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials"
)

// main starts a gRPC server and waits for connection
func main() {
  // create a listener on TCP port 7777
  lis, err := net.Listen("tcp", fmt.Sprintf("%s:%d", "localhost", 7777))
  if err != nil {
    log.Fatalf("failed to listen: %v", err)
  }

  // create a server instance
  s := api.Server{}

  // Create the TLS credentials
  creds, err := credentials.NewServerTLSFromFile("cert/server.crt", "cert/server.key")
  if err != nil {
    log.Fatalf("could not load TLS keys: %s", err)
  }

  // Create an array of gRPC options with the credentials
  opts := []grpc.ServerOption{grpc.Creds(creds)}

  // create a gRPC server object
  grpcServer := grpc.NewServer(opts...)

  // attach the Ping service to the server
  api.RegisterPingServer(grpcServer, &s)

  // start the server
  if err := grpcServer.Serve(lis); err != nil {
    log.Fatalf("failed to serve: %s", err)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

那么是什么改变了呢？

*   您从您的证书和密钥文件中创建了一个[凭证](https://godoc.org/google.golang.org/grpc/credentials)对象(名为`creds`);
*   您创建了一个`grpc.ServerOption`数组，并在其中放置了您的凭证对象；
*   当创建 grpc 服务器时，您为构造器提供了您的数组`grpc.ServerOption`；
*   您一定已经注意到，您需要精确地指定您的服务器绑定到的 IP，以便该 IP 与证书中使用的 FQDN 相匹配。

注意，`grpc.NewServer()`是一个变量函数，所以可以传递任意数量的尾随参数。您创建了一组选项，以便我们稍后可以添加其他选项。

如果你现在编译你的`server`，用你已经有的`client`，连接就不行了，双方都会抛出错误。

*   服务器报告客户端未与 TLS 握手:

```
2006/01/02 15:04:05 grpc: Server.Serve failed to complete security handshake from "localhost:64018": tls: first record does not look like a TLS handshake 
```

Enter fullscreen mode Exit fullscreen mode

*   客户端在执行任何操作之前关闭了其连接:

```
2006/01/02 15:04:05 transport: http2Client.notifyError got notified that the client transport was broken read tcp localhost:64018->127.0.0.1:7777: read: connection reset by peer.
2006/01/02 15:04:05 Error when calling SayHello: rpc error: code = Internal desc = transport is closing 
```

Enter fullscreen mode Exit fullscreen mode

您需要在客户端使用完全相同的证书文件。所以编辑`client/main.go`文件:

```
package main

import (
  "log"

  "gitlab.com/pantomath-io/demo-grpc/api"
  "golang.org/x/net/context"
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials"
)

func main() {
  var conn *grpc.ClientConn

// Create the client TLS credentials
  creds, err := credentials.NewClientTLSFromFile("cert/server.crt", "")
  if err != nil {
    log.Fatalf("could not load tls cert: %s", err)
  }

  // Initiate a connection with the server
  conn, err = grpc.Dial("localhost:7777", grpc.WithTransportCredentials(creds))
  if err != nil {
    log.Fatalf("did not connect: %s", err)
  }
  defer conn.Close()

  c := api.NewPingClient(conn)

  response, err := c.SayHello(context.Background(), &api.PingMessage{Greeting: "foo"})
  if err != nil {
    log.Fatalf("error when calling SayHello: %s", err)
  }
  log.Printf("Response from server: %s", response.Greeting)
} 
```

Enter fullscreen mode Exit fullscreen mode

客户端的变化与服务器端的变化非常相似:

*   您用证书文件创建了一个[凭证](https://godoc.org/google.golang.org/grpc/credentials)对象。注意客户端**不使用**的证书密钥，这个密钥是服务器私有的；
*   您使用 credentials 对象向`grpc.Dial()`函数添加了一个选项。注意，`grpc.Dial()`函数也是一个变量函数，所以它接受任意数量的选项；
*   相同的服务器注意事项适用于客户端:您需要使用与证书中使用的相同的 FQDN 连接到服务器，否则传输身份验证握手将失败。

双方都使用凭证，所以他们应该能够像以前一样进行对话，但是是以加密的方式。可以编译代码:

```
$ make 
```

Enter fullscreen mode Exit fullscreen mode

并在单独的终端中运行两端:

```
$ bin/server
2006/01/02 15:04:05 Receive message foo 
```

Enter fullscreen mode Exit fullscreen mode

```
$ bin/client
2006/01/02 15:04:05 Response from server: bar 
```

Enter fullscreen mode Exit fullscreen mode

## 识别客户端

*git 标签:* [add-auth](https://gitlab.com/pantomath-io/demo-grpc/tree/add-auth)

[![](img/27cd5571f34c4af83fd3dc52cb3f9cc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ja4wNIBy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A9hIUb4TpxYkHehxMb7KpEw.jpeg) 
*照片由 [chuttersnap](https://unsplash.com/photos/984e1Fk4E04?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T9】*

gRPC 服务器的另一个有趣的特性是能够拦截来自客户机的请求。客户端可以在传输层注入信息。您可以使用该功能来识别您的客户端，因为 SSL 实现对服务器进行身份验证(通过证书)，而不是对客户端进行身份验证(您的所有客户端都使用相同的证书)。

因此，您将更新客户端以在每个调用中注入元数据(如登录名和密码)，并更新服务器端以检查每个传入调用的凭证。

在客户端，您只需要在您的`grpc.Dial()`调用上指定一个`DialOption`。但是 T2 有一些限制。编辑你的`client/main.go`文件:

```
package main

import (
  "log"

  "gitlab.com/pantomath-io/demo-grpc/api"
  "golang.org/x/net/context"
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials"
)

// Authentication holds the login/password
type Authentication struct {
  Login    string
  Password string
}

// GetRequestMetadata gets the current request metadata
func (a *Authentication) GetRequestMetadata(context.Context, ...string) (map[string]string, error) {
  return map[string]string{
    "login":    a.Login,
    "password": a.Password,
  }, nil
}

// RequireTransportSecurity indicates whether the credentials requires transport security
func (a *Authentication) RequireTransportSecurity() bool {
  return true
}

func main() {
  var conn *grpc.ClientConn

// Create the client TLS credentials
  creds, err := credentials.NewClientTLSFromFile("cert/server.crt", "")
  if err != nil {
    log.Fatalf("could not load tls cert: %s", err)
 }

  // Setup the login/pass
  auth := Authentication{
    Login:    "john",
    Password: "doe",
  }

  // Initiate a connection with the server
  conn, err = grpc.Dial("localhost:7777", grpc.WithTransportCredentials(creds), grpc.WithPerRPCCredentials(&auth))
  if err != nil {
    log.Fatalf("did not connect: %s", err)
   }
   defer conn.Close()

  c := api.NewPingClient(conn)

  response, err := c.SayHello(context.Background(), &api.PingMessage{Greeting: "foo"})
  if err != nil {
    log.Fatalf("error when calling SayHello: %s", err)
  }
  log.Printf("Response from server: %s", response.Greeting)
} 
```

Enter fullscreen mode Exit fullscreen mode

*   您可以定义一个结构来保存要在 rcp 调用中注入的字段的集合。在我们的例子中，只是一个登录名和密码，但是你可以想象任何你想要的字段；
*   `auth`变量保存您将要使用的值；
*   您使用`grpc.WithPerRPCCredentials()`函数为`grpc.Dial()`函数创建一个`DialOption`对象；
*   请注意，`grpc.WithPerRPCCredentials()`函数将一个接口作为参数，因此您的`Authentication`结构应该符合该接口。从[文档](https://godoc.org/google.golang.org/grpc/credentials#PerRPCCredentials)，你知道你应该在你的结构上实现两个方法:`GetRequestMetadata`和`RequireTransportSecurity`。
*   所以您定义了`GetRequestMetadata`函数，该函数只返回您的`Authentication`结构的映射；
*   最后，您定义了`RequireTransportSecurity`函数，它告诉您的`grpc`客户端是否应该在传输层注入元数据。在我们当前的例子中，它总是返回 true，但是你可以让它返回一个配置布尔值。

客户端在调用服务器时会推送额外的数据，但是服务器现在不在乎。所以你需要告诉他检查这些元数据。打开`server/main.go`并更新:

```
package main

import (
  "fmt"
  "log"
  "net"
  "strings"

  "golang.org/x/net/context"

  "gitlab.com/pantomath-io/demo-grpc/api"
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials"
  "google.golang.org/grpc/metadata"
)

// private type for Context keys
type contextKey int

const (
  clientIDKey contextKey = iota
)

// authenticateAgent check the client credentials
func authenticateClient(ctx context.Context, s *api.Server) (string, error) {
  if md, ok := metadata.FromIncomingContext(ctx); ok {
    clientLogin := strings.Join(md["login"], "")
    clientPassword := strings.Join(md["password"], "")

    if clientLogin != "john" {
      return "", fmt.Errorf("unknown user %s", clientLogin)
    }
    if clientPassword != "doe" {
      return "", fmt.Errorf("bad password %s", clientPassword)
    }

    log.Printf("authenticated client: %s", clientLogin)
    return "42", nil
  }

  return "", fmt.Errorf("missing credentials")
}

// unaryInterceptor calls authenticateClient with current context
func unaryInterceptor(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
  s, ok := info.Server.(*api.Server)
  if !ok {
    return nil, fmt.Errorf("unable to cast server")
  }
  clientID, err := authenticateClient(ctx, s)
  if err != nil {
    return nil, err
  }

  ctx = context.WithValue(ctx, clientIDKey, clientID)
  return handler(ctx, req)
}

// main start a gRPC server and waits for connection
func main() {
  // create a listener on TCP port 7777
  lis, err := net.Listen("tcp", fmt.Sprintf("%s:%d", "localhost", 7777))
  if err != nil {
    log.Fatalf("failed to listen: %v", err)
  }

  // create a server instance
  s := api.Server{}

  // Create the TLS credentials
  creds, err := credentials.NewServerTLSFromFile("cert/server.crt", "cert/server.key")
  if err != nil {
    log.Fatalf("could not load TLS keys: %s", err)
  }

  // Create an array of gRPC options with the credentials
  opts := []grpc.ServerOption{grpc.Creds(creds),
    grpc.UnaryInterceptor(unaryInterceptor)}

  // create a gRPC server object
  grpcServer := grpc.NewServer(opts...)

  // attach the Ping service to the server
  api.RegisterPingServer(grpcServer, &s)

  // start the server
  if err := grpcServer.Serve(lis); err != nil {
    log.Fatalf("failed to serve: %s", err)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我再一次为你解释一下:

*   你添加一个新的`grpc.ServerOption`到你之前创建的数组中(现在明白为什么它是一个数组了吧？):`grpc.UnaryInterceptor`。你传递一个对函数的[引用](https://godoc.org/google.golang.org/grpc#UnaryServerInterceptor)给那个函数，所以它知道该调用谁。主代码的其余部分不变；
*   你必须定义`unaryInterceptor`函数，考虑到它有一堆参数:

1.  一个`context.Context`对象，包含您的数据，并且将在请求的整个生命周期内存在；
2.  一个`interface{}`，它是 RPC 调用的入站参数；
3.  一个`UnaryServerInfo` struct，它包含一堆关于调用的信息(比如`Server`抽象对象，以及客户端的方法调用)；
4.  一个`UnaryHandler` struct，它是由`UnaryServerInterceptor`调用的处理程序，用来完成一元 RPC 的正常执行(即当`UnaryInterceptor`返回时发生什么的处理程序)。

*   `unaryInterceptor`函数确保`grpc.UnaryServerInfo`具有正确的服务器抽象，并调用认证函数`authenticateClient`；
*   您用您的认证逻辑定义了`authenticateClient`函数——在这个例子中非常非常简单。注意，它接收`context.Context`作为参数，并从中提取元数据。它检查用户，并返回其 ID(以一个`string`的形式)，带有一个假设的错误。
*   如果`unaryInterceptor`没有从`authenticateClient`函数中得到错误，它会将`clientID`推入`context.Context`对象中，以便执行链的其余部分可以使用它(还记得`handler`将`context.Context`对象作为参数获取吗？);
*   注意，您创建了您的`type`和`const`来引用`context.Context`地图中的`clientID`。这只是一种避免命名冲突并允许不断引用的简便方法。

可以编译代码:

```
$ make 
```

Enter fullscreen mode Exit fullscreen mode

并在单独的终端中运行两端:

```
$ bin/server
2006/01/02 15:04:05 authenticated client: john
2006/01/02 15:04:05 Receive message foo 
```

Enter fullscreen mode Exit fullscreen mode

```
$ bin/client
2006/01/02 15:04:05 Response from server: bar 
```

Enter fullscreen mode Exit fullscreen mode

显然，您的身份验证逻辑可能会更智能，将凭据与数据库进行比较。最简单的部分是:您的认证函数获取您对一个`Server`的抽象，这个结构可以保存您的数据库处理程序。

## 开放休息

*去标签* [加馀物](https://gitlab.com/pantomath-io/demo-grpc/tree/add-rest)

[![](img/a3da63d7d4711b02c095750dfd9e95f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1wmYUI0R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AdpCAELNImICQxw8UxA0bsA.jpeg) 
*照片由[里奥霍奇](https://unsplash.com/photos/nNPONh2aXeQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

最后一件事:你有一个非常整洁的服务器、客户端和协议；序列化、加密和认证。但是有一个重要的限制:你的客户端需要符合 gRPC，也就是在[支持平台](https://grpc.io/about/#osp)的列表中。为了避免这个限制，我们可以向 REST 网关开放服务器，允许 REST 客户端执行请求。幸运的是，有一个 gRPC [协议插件](https://github.com/grpc-ecosystem/grpc-gateway)可以生成一个反向代理服务器，将 RESTful JSON API 转换成 gRPC。我们可以使用几行纯 Go 代码来服务反向代理。

因此，编辑您的`api/api.proto`文件，添加一些额外的信息:

```
syntax = "proto3";
package api;

import "google/api/annotations.proto";

message PingMessage {
  string greeting = 1;
}

service Ping {
  rpc SayHello(PingMessage) returns (PingMessage) {
    option (google.api.http) = {
      post: "/1/ping"
      body: "*"
    };
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`annotations.proto`导入允许`protoc`理解文件后面设置的`option`。而`option`定义了该方法和到端点的路径。

更新`Makefile`为这个新的`Protobuf`编译添加一个目标:

```
SERVER_OUT := "bin/server"
CLIENT_OUT := "bin/client"
API_OUT := "api/api.pb.go"
API_REST_OUT := "api/api.pb.gw.go"
PKG := "gitlab.com/pantomath-io/demo-grpc"
SERVER_PKG_BUILD := "${PKG}/server"
CLIENT_PKG_BUILD := "${PKG}/client"
PKG_LIST := $(shell go list ${PKG}/... | grep -v /vendor/)

.PHONY: all api server client

all: server client

api/api.pb.go: api/api.proto

 -I api/ \
    -I${GOPATH}/src \
    -I${GOPATH}/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
    --go_out=plugins=grpc:api \
    api/api.proto

api/api.pb.gw.go: api/api.proto

 -I api/ \
    -I${GOPATH}/src \
    -I${GOPATH}/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
    --grpc-gateway_out=logtostderr=true:api \
    api/api.proto

api: api/api.pb.go api/api.pb.gw.go ## Auto-generate grpc go sources

dep: ## Get the dependencies

 get -v -d ./...

server: dep api ## Build the binary file for server

 build -i -v -o $(SERVER_OUT) $(SERVER_PKG_BUILD)

client: dep api ## Build the binary file for client

 build -i -v -o $(CLIENT_OUT) $(CLIENT_PKG_BUILD)

clean: ## Remove previous builds

 $(SERVER_OUT) $(CLIENT_OUT) $(API_OUT) $(API_REST_OUT)

help: ## Display this help screen

 -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}' 
```

Enter fullscreen mode Exit fullscreen mode

为网关生成 Go 代码(会生成文件`api/api.pb.gw.go`—就像`api/api.pb.go`一样，不要编辑，会通过编译更新):

```
$ make api 
```

Enter fullscreen mode Exit fullscreen mode

服务器端的变化更重要。`grpc.Serve()`函数是一个阻塞函数，只在出错时返回(或者被信号杀死)。因为我们需要启动另一个服务器(REST 接口)，所以我们需要这个调用是非阻塞的。幸运的是，我们有 **goroutines** 就是为了这个。并且在认证上有一个技巧。因为 REST 网关只是一个反向代理，所以从 gRPC 的角度来看，它充当客户端。因此，它需要在拨号服务器时使用一个`WithPerRPCCredentials`选项。

前往您的`server/main.go`文件:

```
package main

import (
  "fmt"
  "log"
  "net"
  "net/http"
  "strings"

  "github.com/grpc-ecosystem/grpc-gateway/runtime"
  "golang.org/x/net/context"
  "gitlab.com/pantomath-io/demo-grpc/api"
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials"
  "google.golang.org/grpc/metadata"
)

// private type for Context keys
type contextKey int

const (
  clientIDKey contextKey = iota
)

func credMatcher(headerName string) (mdName string, ok bool) {
  if headerName == "Login" || headerName == "Password" {
    return headerName, true
  }
  return "", false
}

// authenticateAgent check the client credentials
func authenticateClient(ctx context.Context, s *api.Server) (string, error) {
  if md, ok := metadata.FromIncomingContext(ctx); ok {
    clientLogin := strings.Join(md["login"], "")
    clientPassword := strings.Join(md["password"], "")

    if clientLogin != "john" {
      return "", fmt.Errorf("unknown user %s", clientLogin)
    }
    if clientPassword != "doe" {
      return "", fmt.Errorf("bad password %s", clientPassword)
    }

    log.Printf("authenticated client: %s", clientLogin)
    return "42", nil
  }
  return "", fmt.Errorf("missing credentials")
}

// unaryInterceptor call authenticateClient with current context
func unaryInterceptor(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
  s, ok := info.Server.(*api.Server)
  if !ok {
    return nil, fmt.Errorf("unable to cast server")
  }
  clientID, err := authenticateClient(ctx, s)
  if err != nil {
    return nil, err
  }

  ctx = context.WithValue(ctx, clientIDKey, clientID)
  return handler(ctx, req)
}

func startGRPCServer(address, certFile, keyFile string) error {
  // create a listener on TCP port
  lis, err := net.Listen("tcp", address)
  if err != nil {
    return fmt.Errorf("failed to listen: %v", err)
  }

  // create a server instance
  s := api.Server{}

  // Create the TLS credentials
  creds, err := credentials.NewServerTLSFromFile(certFile, keyFile)
  if err != nil {
    return fmt.Errorf("could not load TLS keys: %s", err)
  }

  // Create an array of gRPC options with the credentials
  opts := []grpc.ServerOption{grpc.Creds(creds),
    grpc.UnaryInterceptor(unaryInterceptor)}

  // create a gRPC server object
  grpcServer := grpc.NewServer(opts...)

  // attach the Ping service to the server
  api.RegisterPingServer(grpcServer, &s)

  // start the server
  log.Printf("starting HTTP/2 gRPC server on %s", address)
  if err := grpcServer.Serve(lis); err != nil {
    return fmt.Errorf("failed to serve: %s", err)
  }

  return nil
}

func startRESTServer(address, grpcAddress, certFile string) error {
  ctx := context.Background()
  ctx, cancel := context.WithCancel(ctx)
  defer cancel()

  mux := runtime.NewServeMux(runtime.WithIncomingHeaderMatcher(credMatcher))

  creds, err := credentials.NewClientTLSFromFile(certFile, "")
  if err != nil {
    return fmt.Errorf("could not load TLS certificate: %s", err)
  }

  // Setup the client gRPC options
  opts := []grpc.DialOption{grpc.WithTransportCredentials(creds)}

  // Register ping
  err = api.RegisterPingHandlerFromEndpoint(ctx, mux, grpcAddress, opts)
  if err != nil {
    return fmt.Errorf("could not register service Ping: %s", err)
  }

  log.Printf("starting HTTP/1.1 REST server on %s", address)
  http.ListenAndServe(address, mux)

  return nil
}

// main start a gRPC server and waits for connection
func main() {
  grpcAddress := fmt.Sprintf("%s:%d", "localhost", 7777)
  restAddress := fmt.Sprintf("%s:%d", "localhost", 7778)
  certFile := "cert/server.crt"
  keyFile := "cert/server.key"

  // fire the gRPC server in a goroutine
  go func() {
    err := startGRPCServer(grpcAddress, certFile, keyFile)
    if err != nil {
      log.Fatalf("failed to start gRPC server: %s", err)
    }
  }()

  // fire the REST server in a goroutine
  go func() {
    err := startRESTServer(restAddress, grpcAddress, certFile)
    if err != nil {
      log.Fatalf("failed to start gRPC server: %s", err)
    }
  }()

  // infinite loop
  log.Printf("Entering infinite loop")
  select {}
} 
```

Enter fullscreen mode Exit fullscreen mode

发生了什么事？

*   你用一个专用函数(`startGRPCServer`)把创建 gRPC 服务器的所有代码都移到了一个 goroutine 中，所以它不会阻塞`main`；
*   您创建了一个新的具有专用函数(`startRESTServer`)的 goroutine，其中您创建了一个 HTTP/1.1 服务器；
*   在创建 REST 网关的`startRESTServer`中，首先获取`context.Context`背景对象(即上下文树的根)。然后，创建一个请求多路复用器对象`mux`，并带有一个选项:`runtime.WithIncomingHeaderMatcher`。该选项将函数引用作为参数，`credMatch`，并为来自传入请求的每个 HTTP 头调用。该函数评估是否应该将 HTTP 头传递给 gRPC 上下文；
*   您定义了`credMatch`函数来匹配凭证头，允许它们成为 gRPC 上下文中的元数据。这就是您的身份验证的工作方式，因为反向代理使用它在连接到 gRPC 服务器时收到的 HTTP 头；
*   您还创建了一个`credentials.NewClientTLSFromFile`，用作`grpc.DialOption`，就像您在客户端所做的一样；
*   你注册你的 api 端点，也就是说，你使用上下文和 gRPC 选项在你的多路复用器、你的 gRPC 服务器之间建立链接；
*   最后，您启动一个 HTTP/1.1 服务器，并等待传入的连接；
*   除了你的 goroutine 之外，你还使用了一个阻塞调用，这样你的程序就不会马上结束。

现在构建整个项目，这样就可以测试 REST 接口:

```
$ make 
```

Enter fullscreen mode Exit fullscreen mode

并在单独的终端中运行两端:

```
$ bin/server
2006/01/02 15:04:05 Entering infinite loop
2006/01/02 15:04:05 starting HTTP/1.1 REST server on localhost:7778
2006/01/02 15:04:05 starting HTTP/2 gRPC server on localhost:7777
2006/01/02 15:04:05 authenticated client: john
2006/01/02 15:04:05 Receive message foo

$ curl -H "login:john" -H "password:doe" -X POST -d '{"greeting":"foo"}' '
{"greeting":"bar"} 
```

Enter fullscreen mode Exit fullscreen mode

## 最后一份礼物…

*git 标签:* [add-swagger](https://gitlab.com/pantomath-io/demo-grpc/tree/add-swagger)

[![](img/a06649c90a2896c4e780e7f296cbe45c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3rQCyG53--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Alg78b_518eFveLDKDsA5hQ.jpeg) 
*照片由[洛伦佐·卡斯塔尼奥内](https://unsplash.com/photos/ETNcibiiWOs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍于[Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T9】*

REST gateway 很酷，但是用它生成文档会更酷，对吗？

你可以免费这样做，使用一个[协议插件](https://github.com/grpc-ecosystem/grpc-gateway)生成一个 [swagger](https://swagger.io/) json 文件:

```
protoc -I api/ \
  -I${GOPATH}/src \
  -I${GOPATH}/src/github.com/grpc-ecosystem/grpc-gateway/third_party/googleapis \
  --swagger_out=logtostderr=true:api \
  api/api.proto 
```

Enter fullscreen mode Exit fullscreen mode

这将生成一个`api/api.swagger.json`文件。由于所有生成的代码都来自 Protobuf 编译，你不应该编辑它，但你可以使用它，当你改变你的定义文件时它可以更新它。

您可以在`Makefile`中添加编译命令。

## 结论

您有一个功能完整的 gRPC 客户机和服务器，带有 SSL 加密和认证、客户机识别和 REST 网关(带有它的 swagger 文件)。从这里去哪里？

你可以在 REST 网关上推一点，让它成为 HTTPS 而不是 HTTP。很明显，除了更多的`service`，你还可以在 Protobuf 上添加更复杂的数据结构。你可以从 HTTP/2 特性中受益，比如[流](https://grpc.io/docs/guides/concepts.html#bidirectional-streaming-rpc)，或者从客户端到服务器，或者从服务器到客户端，或者是双向的(但那只是针对 gRPC，其他的都不行，基于 HTTP/1.1)。

*非常感谢[查尔斯·弗朗索瓦丝](https://dev.to/loderunner)和[https://gitlab.com/pantomath-io/demo-grpc](https://gitlab.com/pantomath-io/demo-grpc)共同撰写本文。*

* * *

我们目前正在开发 Pantomath。Pantomath 是一款现代化的开源监控解决方案，专为提高性能而设计，能够弥合公司各个层面的差距。基础设施的良好运行是每个人的事。跟上[项目](//goo.gl/tcxtXq)T3】