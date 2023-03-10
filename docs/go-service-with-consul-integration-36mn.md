# 咨询集成 Go 服务

> 原文：<https://dev.to/dzeban/go-service-with-consul-integration-36mn>

在通常用 Go 编写的无状态微服务的世界里，我们需要发现它们。这就是哈希公司的领事帮忙的地方。服务在 Consul 中注册，因此其他服务可以通过简单的 DNS 或 HTTP 查询发现它们。

Go 有一个 Consul 客户端库，唉，我没有看到任何如何将其集成到您的服务中的真实示例。所以在这里我将向你展示如何做到这一点。

我将编写一个服务，它将服务于某个 HTTP 端点，并将服务于键值数据——我相信这类似于人们现在编写的许多现有的微服务。我们的叫做`webkv`，在 Github 上是[。选择](https://github.com/alexdzyoba/webkv)[“v1”标签](https://github.com/alexdzyoba/webkv/tree/v1)，一切就绪。

该服务将在 consult 中用 TTL 检查注册自己，这将检查内部健康状态，并向 consult 发送类似心跳的信号。如果 Consul 在 TTL 时间间隔内没有收到我们服务的信号，它会将其标记为失败，并将其从查询结果中删除。

补充说明:Consul 也有简单的端口检查，Consul 代理会根据端口可用性判断服务的健康状况。虽然它简单得多，例如，您不必在代码中添加任何东西，但它没有 TTL 检查那么强大。通过 TTL 检查，您可以检查服务的内部状态，与简单可用性相比，这是一个巨大的优势——您可以接受查询，但您的数据可能会过时或无效。此外，通过 TTL 检查，服务状态不仅可以是二进制状态(好/坏),还可以是警告。

好吧，说重点！`webkv`的“v1”版本仅使用标准库和最少的依赖项，如 Redis 客户端和 Consul API lib。稍后，我将扩展它，添加其他细节，如 Prometheus 集成、结构化日志记录和 sane 配置管理。

## 基础 Web 服务

让我们从一个基本的 web 服务开始，它将提供来自 Redis 的键值数据。

首先，解析`port`、`ttl`和`addrs`命令行标志。最后一个是用`;`分隔的 Redis 地址列表。

```
func main() {
    port := flag.Int("port", 8080, "Port to listen on")
    addrsStr := flag.String("addrs", "", "(Required) Redis addrs (may be delimited by ;)")
    ttl := flag.Duration("ttl", time.Second*15, "Service TTL check duration")
    flag.Parse()

    if len(*addrsStr) == 0 {
        fmt.Fprintln(os.Stderr, "addrs argument is required")
        flag.PrintDefaults()
        os.Exit(1)
    }

    addrs := strings.Split(*addrsStr, ";") 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们创建一个应该实现 [`Handler`](https://golang.org/pkg/net/http/#Handler) 接口的服务并启动它。

```
 s, err := service.New(addrs, *ttl)
    if err != nil {
        log.Fatal(err)
    }
    http.Handle("/", s)

    l := fmt.Sprintf(":%d", *port)
    log.Print("Listening on ", l)
    log.Fatal(http.ListenAndServe(l, nil)) 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的。现在让我们看看服务本身。

```
import (
    "time"

    "github.com/go-redis/redis"
)

type Service struct {
    Name        string
    TTL         time.Duration
    RedisClient redis.UniversalClient
} 
```

Enter fullscreen mode Exit fullscreen mode

`Service`是一个保存名称、TTL 和 Redis 客户端处理程序的类型。它是这样实例化的:

```
func New(addrs []string, ttl time.Duration) (*Service, error) {
    s := new(Service)
    s.Name = "webkv"
    s.TTL = ttl
    s.RedisClient = redis.NewUniversalClient(&redis.UniversalOptions{
        Addrs: addrs,
    })

    ok, err := s.Check()
    if !ok {
        return nil, err
    }

    return s, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

`Check`方法发出`PING` Redis 命令来检查我们是否正常。这将在以后的领事注册中使用。

```
func (s *Service) Check() (bool, error) {
    _, err := s.RedisClient.Ping().Result()
    if err != nil {
        return false, err
    }
    return true, nil 
```

Enter fullscreen mode Exit fullscreen mode

现在是请求处理将调用的`ServeHTTP`方法的实现:

```
func (s *Service) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    status := 200

    key := strings.Trim(r.URL.Path, "/")
    val, err := s.RedisClient.Get(key).Result()
    if err != nil {
        http.Error(w, "Key not found", http.StatusNotFound)
        status = 404
    }

    fmt.Fprint(w, val)
    log.Printf("url=\"%s\" remote=\"%s\" key=\"%s\" status=%d\n",
        r.URL, r.RemoteAddr, key, status)
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们所做的是从请求中检索 URL 路径，并将其用作 Redis“GET”命令的键。之后，如果出现错误，我们将返回值或 404。最后，我们用一个快速而肮脏的结构化日志消息以 [logfmt 格式](https://brandur.org/logfmt)记录请求。

启动它:

```
$ ./webkv -addrs 'localhost:6379'
2017/12/13 21:44:15 Listening on :8080 
```

Enter fullscreen mode Exit fullscreen mode

查询一下:

```
$ curl 'localhost:8080/blink'
182 
```

Enter fullscreen mode Exit fullscreen mode

并查看日志消息:

```
2017/12/13 21:44:29 url="/blink" remote="[::1]:35020" key="blink" status=200 
```

Enter fullscreen mode Exit fullscreen mode

## 执政官集成

现在让我们通过 Consul 发现我们的服务。Consul 有简单的 HTTP API 来注册服务，您可以通过“net/http”直接使用这些服务，但是我们将使用它的 [Go 库](https://godoc.org/github.com/hashicorp/consul/api)。

领事围棋库没有例子，但是，有测试！测试很好，不仅因为它给你对库的信心，对你的代码结构和 API 的健全性的认可，最后，还有一组使用示例。这里有一个来自 Consul API 测试套件的服务注册和 TTL 检查的例子。

查看这些测试，我们可以看出，我们通过创建一个`Client`来与 Consul 进行交互，然后获取特定的[端点](https://www.consul.io/api/index.html)的句柄，如`/agent`或`/kv`。对于每个端点，都有相应的 Go 类型。代理端点负责服务注册和发送运行状况检查。为了存储一个`Agent`句柄，我们用一个新指针
扩展了我们的`Service`类型

```
import (
    consul "github.com/hashicorp/consul/api"
)

type Service struct {
    Name        string
    TTL         time.Duration
    RedisClient redis.UniversalClient
    ConsulAgent *consul.Agent
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在服务“构造函数”中，我们添加 Consul 代理句柄的创建:

```
func New(addrs []string, ttl time.Duration) (*Service, error) {
    ...
    c, err := consul.NewClient(consul.DefaultConfig())
    if err != nil {
        return nil, err
    }
    s.ConsulAgent = c.Agent() 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们使用代理注册我们的服务:

```
 serviceDef := &consul.AgentServiceRegistration{
        Name: s.Name,
        Check: &consul.AgentServiceCheck{
            TTL: s.TTL.String(),
        },
    }

    if err := s.ConsulAgent.ServiceRegister(serviceDef); err != nil {
        return nil, err
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里的关键是`Check`部分，我们告诉 Consul 它应该如何检查我们的服务。在我们的例子中，我们说我们自己将向 Consul 发送类似心跳的信号，以便它在 TTL 之后标记我们的服务失败。失败的服务不会作为 DNS 或 HTTP API 查询的一部分返回。

服务注册后，我们必须发送一个 TTL 检查信号，包括通过、失败或警告类型。我们必须定期及时地发送它，以避免 TTL 造成的服务失败。我们将在单独的 goroutine:

```
go s.UpdateTTL(s.Check) 
```

Enter fullscreen mode Exit fullscreen mode

`UpdateTTL`方法使用 [`time.Ticker`](https://golang.org/pkg/time/#Ticker) 定期调用实际更新函数:

```
func (s *Service) UpdateTTL(check func() (bool, error)) {
    ticker := time.NewTicker(s.TTL / 2)
    for range ticker.C {
        s.update(check)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`check`参数是返回服务状态的函数。基于其结果，我们发送通过或失败检查:
go

```
func (s *Service) update(check func() (bool, error)) {
    ok, err := check()
    if !ok {
        log.Printf("err=\"Check failed\" msg=\"%s\"", err.Error())
        if agentErr := s.ConsulAgent.FailTTL("service:"+s.Name, err.Error()); agentErr != nil {
            log.Print(agentErr)
        }
    } else {
        if agentErr := s.ConsulAgent.PassTTL("service:"+s.Name, ""); agentErr != nil {
            log.Print(agentErr)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们传递给 goroutine 的 Check 函数是我们之前在创建服务时使用的函数，它只返回 Redis PING 命令的 bool 状态。

就是这样！这就是所有这些协同工作的方式:

*   我们发射`webkv`
*   它连接到 Redis 并开始在给定的端口服务
*   它连接到咨询代理，并注册带有 TTL 检查的服务
*   每 TTL/2 秒，我们通过 PINGing Redis 和发送 Pass check 来检查服务状态
*   如果 Redis 连接失败，我们会检测到它并发送一个失败检查，这将从 DNS 和 HTTP 查询中删除我们的服务实例，以避免返回错误或无效数据

要看到它的运行，你需要启动一个领事和 Redis。你可以用`consul agent -dev`启动 Consul 或者启动一个普通集群。如何推出 Redis 取决于你的发行版，在我的 Fedora 里，只是`systemctl start redis`。

现在像这样发射`webkv`:

```
$ ./webkv -addrs localhost:6379 -port 8888
2017/12/14 19:00:29 Listening on :8888 
```

Enter fullscreen mode Exit fullscreen mode

查询咨询服务:

```
$ dig +noall +answer @127.0.0.1 -p 8600 webkv.service.dc1.consul
webkv.service.dc1.consul. 0 IN A 127.0.0.1

$ curl localhost:8500/v1/health/service/webkv?passing
[
    {
        "Node": {
            "ID": "a4618035-c73d-9e9e-2b83-24ece7c24f45",
            "Node": "alien",
            "Address": "127.0.0.1",
            "Datacenter": "dc1",
            "TaggedAddresses": {
                "lan": "127.0.0.1",
                "wan": "127.0.0.1"
            },
            "Meta": {
                "consul-network-segment": ""
            },
            "CreateIndex": 5,
            "ModifyIndex": 6
        },
        "Service": {
            "ID": "webkv",
            "Service": "webkv",
            "Tags": [],
            "Address": "",
            "Port": 0,
            "EnableTagOverride": false,
            "CreateIndex": 15,
            "ModifyIndex": 37
        },
        "Checks": [
            {
                "Node": "alien",
                "CheckID": "serfHealth",
                "Name": "Serf Health Status",
                "Status": "passing",
                "Notes": "",
                "Output": "Agent alive and reachable",
                "ServiceID": "",
                "ServiceName": "",
                "ServiceTags": [],
                "Definition": {},
                "CreateIndex": 5,
                "ModifyIndex": 5
            },
            {
                "Node": "alien",
                "CheckID": "service:webkv",
                "Name": "Service 'webkv' check",
                "Status": "passing",
                "Notes": "",
                "Output": "",
                "ServiceID": "webkv",
                "ServiceName": "webkv",
                "ServiceTags": [],
                "Definition": {},
                "CreateIndex": 15,
                "ModifyIndex": 141
            }
        ]
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们停止 Redis，我们将看到日志消息

```
...
2017/12/14 19:29:19 err="Check failed" msg="EOF"
2017/12/14 19:29:27 err="Check failed" msg="dial tcp [::1]:6379: getsockopt: connection refused"
... 
```

Enter fullscreen mode Exit fullscreen mode

领事没有回复我们服务

```
$ dig +noall +answer @127.0.0.1 -p 8600 webkv.service.dc1.consul
$ # empty reply

$ curl localhost:8500/v1/health/service/webkv?passing
[] 
```

Enter fullscreen mode Exit fullscreen mode

再次启动 Redis 将使服务变得健康。

所以，基本上就是这个了——集成了 Consul 的基本 Web 服务，用于服务发现和健康检查。在 github.com/alexdzyoba/webkv 查看完整的源代码。下一次，我们将添加指标导出，用于监控我们与 Prometheus 的服务。