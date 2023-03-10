# 就 HTTP 性能而言，Go 与 NET Core

> 原文：<https://dev.to/kataras/go-vsnet-core-in-terms-of-http-performance>

[![article cover](img/138770e397a30eae12d1f60f11f5c365.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iy9PMasF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/938/1%2A60T60jSMO5yxUqJQmLZa4A.png)

朋友们好！

最近我听到了很多关于新的。NET 核心及其性能，尤其是在 web 服务器上。

我不想开始比较两种不同的东西，所以我做了相当长时间的耐心等待一个更稳定的版本。

然而，本周一，[微软宣布了。网芯 2.0 版](https://blogs.msdn.microsoft.com/dotnet/2017/08/14/announcing-net-core-2-0/)，所以感觉准备好了！你知道吗？

* * *

正如我们已经提到的，我们将在这里比较两个相同的东西，在应用程序，预期响应和运行时间的稳定性方面，所以我们不会试图在游戏中加入更多的东西，如`JSON`或`XML`编码器和解码器，只是一个简单的文本消息。为了实现公平的比较，我们将在双方都使用 [MVC 架构模式](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)。网芯。

## 先决条件

[Go](https://golang.org) (或 Golang):是一种[快速发展的](http://www.tiobe.com/tiobe-index/)开源编程语言，旨在构建简单、快速、可靠的软件。

支持 MVC 的 web 框架并不多，但幸运的是 Iris 做了这项工作。

Iris:一个快速、简单、高效的面向 Go 的微型 web 框架。它为你的下一个网站、API 或分布式应用程序提供了一个漂亮的表达和易于使用的基础。

[C#](https://en.wikipedia.org/wiki/C_Sharp_%28programming_language%29) :是一种通用的、面向对象的编程语言。其开发团队由[安德斯·海尔斯伯格](https://twitter.com/ahejlsberg)领导。

[。NET Core](https://www.microsoft.com/net/) :在任何平台上用更少的时间开发高性能应用。

从 https://golang.org/dl 下载围棋。网芯来自 https://www.microsoft.com/net/core。

在你下载并安装了这些之后，你将需要 Go 端的 Iris。安装非常简单，只需打开你的终端并执行:

```
go get -u github.com/kataras/iris 
```

# 标杆管理

#### 硬件

*   处理器:英特尔酷睿 i7–4710 HQ CPU @ 2.50 GHz 2.50 GHz
*   内存: **8.00 GB**

#### 软件

*   操作系统:微软**Windows**[版本 **10** .0.15063]，电源计划为“高性能”
*   HTTP 基准工具:[https://github.com/codesenberg/bombardier](https://github.com/codesenberg/bombardier)，最新版本 **1.1**
*   **。网芯**:https://www.microsoft.com/net/core[，最新版本 **2.0**](https://www.microsoft.com/net/core)
*   **Iris**:[https://github.com/kataras/iris](https://github.com/kataras/iris)，最新版本 **8.3** 内置 [go1.8.3](https://golang.org)

这两个应用程序都将只返回请求路径“api/values/{id}”上的文本“value”。

### [T1。网络核心 MVC](#net-core-mvc)

[![Logo designed by Pablo Iglesias](img/93a13ed6625b76983ce6a000c0c19560.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NNJ-hGxD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1250/1%2Av2VJL3-I3bLyuehntuqfng.png)

使用`dotnet new webapi`创建。那个`webapi`模板将为您生成代码，包括对`GET`方法请求的`return “value”`。

源代码

**Program.cs**

```
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace netcore_mvc
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
} 
```

**Startup.cs**

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Options;

namespace netcore_mvc
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvcCore();
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            app.UseMvc();
        }
    }
} 
```

实际**控制器/ValuesController.cs**

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;

namespace netcore_mvc.Controllers
{
    // ValuesController is the equivalent
    // `ValuesController` of the Iris 8.3 mvc application.
    [Route("api/[controller]")]
    public class ValuesController : Controller
    {
        // Get handles "GET" requests to "api/values/{id}".
        [HttpGet("{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // Put handles "PUT" requests to "api/values/{id}".
        [HttpPut("{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // Delete handles "DELETE" requests to "api/values/{id}".
        [HttpDelete("{id}")]
        public void Delete(int id)
        {
        }
    }
} 
```

*开始。NET 核心 web 服务器并运行基准测试工具*

```
$ cd netcore-mvc
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore-mvc
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.. 
```

```
$ bombardier -c 125 -n 5000000 http://localhost:5000/api/values/5
Bombarding http://localhost:5000/api/values/5 with 5000000 requests using 125 connections
 5000000 / 5000000 [================================================================] 100.00% 2m3s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     40226.03    8724.30     161919
  Latency        3.09ms     1.40ms   169.12ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:     8.91MB/s 
```

### 虹膜 MVC

[![Logo designed by SantoshÂ Anand](img/f7647b5f6db9114e64c9ed0daf78ae83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zpqmHy9a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1250/1%2Azf4BjL-7MQNJGikw5E-iNA.png)

源代码

**main . go**T2】

```
package main

import (
    "github.com/kataras/iris"
    "github.com/kataras/iris/_benchmarks/iris-mvc/controllers"
)

func main() {
    app := iris.New()
    app.Controller("/api/values/{id}", new(controllers.ValuesController))
    app.Run(iris.Addr(":5000"))
} 
```

**控制器/值 _ 控制器. go**

```
package controllers

import "github.com/kataras/iris/mvc"

// ValuesController is the equivalent
// `ValuesController` of the .net core 2.0 mvc application.
type ValuesController struct {
    mvc.Controller
}

// Get handles "GET" requests to "api/values/{id}".
func (vc *ValuesController) Get() {
    // id,_ := vc.Params.GetInt("id")
    vc.Ctx.WriteString("value")
}

// Put handles "PUT" requests to "api/values/{id}".
func (vc *ValuesController) Put() {}

// Delete handles "DELETE" requests to "api/values/{id}".
func (vc *ValuesController) Delete() {} 
```

*启动 Go web 服务器并运行基准测试工具*

```
$ cd iris-mvc
$ go run main.go
Now listening on: http://localhost:5000
Application started. Press CTRL+C to shut down. 
```

```
$ bombardier -c 125 -n 5000000 http://localhost:5000/api/values/5
Bombarding http://localhost:5000/api/values/5 with 5000000 requests using 125 connections
 5000000 / 5000000 [================================================================] 100.00% 47s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec    105643.81    7687.79     122564
  Latency        1.18ms   366.55us    22.01ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    19.65MB/s 
```

对于那些通过图像更好理解的人，我也打印了我的屏幕！

点击[这里](https://github.com/kataras/iris/tree/master/_benchmarks/screens)看这些截图。

### 总结

*   完成`5000000 requests`的时间——越小越好。
*   请求/秒—越大越好。
*   延迟——越小越好
*   吞吐量越大越好。
*   内存使用——越小越好。
*   LOC(代码行)——越小越好。

。NET Core MVC 应用程序使用 86 行代码编写，运行了 **2 分 8 秒**，在**平均 3.19 毫秒**延迟和**最大 229.73 毫秒**内，每秒服务 **39311.56** 个请求，所有这些的内存使用量大约为 126MB(不包括 dotnet 主机)。

Iris MVC 应用程序使用 27 行代码编写，运行了 **47 秒**，在**平均 1.18 毫秒**延迟和**最大 22.01 毫秒**延迟内，每秒为 **105643.71** 个请求服务，所有这些的内存使用量大约为 12MB。

> 还有另一个带有模板的基准，滚动到底部。

**更新:2017 年 8 月 20 日**

正如[乔什·克拉克](https://twitter.com/clarkis117)和[斯科特·汉瑟曼"](https://twitter.com/shanselman)"在这个状态上指出[，上。NET Core `Startup.cs`文件中带有`services.AddMvc();`的行可以替换为`services.AddMvcCore();`。我按照他们有用的指示重新运行了基准测试。本文现在包含了。NET 核心应用程序，Josh 和 Scott 都提到了这些变化。](https://twitter.com/shanselman/status/899005786826788865)

twitter 转换:[https://Twitter . com/MakisMaropoulos/status/899113215895982080](https://twitter.com/MakisMaropoulos/status/899113215895982080)

对于那些想要与标准`services.AddMvc();`进行比较的人，你可以通过按下[这里的](https://github.com/kataras/iris/blob/master/_benchmarks/screens/5m_requests_netcore-mvc.png)来查看旧的输出。

## 你能多呆一会儿吗？

让我们再运行一个基准测试，`spawn 1000000 requests`,但是这次我们希望通过视图引擎由模板生成`HTML`。

### [T1。带模板的 NET Core MVC](#net-core-mvc-with-templates)

该测试的源代码文件很长，无法在本文中展示，但是您可以在以下位置查看它们:[https://github . com/kataras/iris/tree/master/_ benchmarks/netcore-MVC-templates](https://github.com/kataras/iris/tree/master/_benchmarks/netcore-mvc-templates)

*开始。NET 核心 web 服务器并运行基准测试工具*

```
$ cd netcore-mvc-templates
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore-mvc-templates
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down. 
```

```
Bombarding http://localhost:5000 with 1000000 requests using 125 connections
 1000000 / 1000000 [===============================================================] 100.00% 1m20s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     11738.60    7741.36     125887
  Latency       10.10ms    22.10ms      1.97s
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    89.03MB/s 
```

### Iris MVC 带模板

该测试的源代码文件很长，无法在本文中展示，但是您可以在以下位置查看它们:[https://github . com/kataras/iris/tree/master/_ benchmarks/iris-MVC-templates](https://github.com/kataras/iris/tree/master/_benchmarks/iris-mvc-templates)

*启动 Go web 服务器并运行基准测试工具*

```
$ cd iris-mvc-templates
$ go run main.go
Now listening on: http://localhost:5000
Application started. Press CTRL+C to shut down. 
```

```
Bombarding http://localhost:5000 with 1000000 requests using 125 connections
 1000000 / 1000000 [================================================================] 100.00% 37s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     26656.76    1944.73      31188
  Latency        4.69ms     1.20ms    22.52ms
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:   192.51MB/s 
```

#### 总结

*   完成`1000000 requests`的时间——越小越好。
*   每秒请求数-越大越好。
*   延迟-越小越好
*   内存使用-越小越好。
*   吞吐量-越大越好。

。NET Core MVC with Templates Application 运行了 **1 分 20 秒**，每秒处理 **11738.60** 个请求，平均**10.10 毫秒**延迟**89.03 兆字节/秒**，最大**1.97 秒**，所有这些的内存使用量大约为 193 兆字节(没有 dotnet 主机)。

Iris MVC 和 Templates 应用程序运行了 **37 秒**，每秒处理 **26656.76** 个请求，在**1.18 毫秒**平均延迟和**22.52 毫秒**最大延迟内**192.51 兆字节/秒**，所有这些的内存使用量大约为 17MB。

# 接下来呢？

从[那里](https://github.com/kataras/iris/tree/master/_benchmarks)下载示例源代码，在你的机器上运行相同的基准测试，然后回到这里与我们分享你的结果！

**感谢大家**的 100%绿色反馈。

页（page 的缩写）S

我正试着回答你们所有人，但是请帮我一个大忙，先检查一下你的问题是否已经在其他地方的问题中得到回答，谢谢，玩得开心！

### 更新:2017 年 8 月 21 日星期一

很多人找到我，说他们想看一篇基于。网芯这次的“低级”**红隼**。
我照做了，通过下面的链接了解 Kestrel 和 Iris 之间的性能差异，它还包含一个会话存储管理基准！

[https://medium . com/@ kataras/iris-go-vs-net-core-kestrel-in-term-of-http-performance-806195 dc93d 5](https://medium.com/@kataras/iris-go-vs-net-core-kestrel-in-terms-of-http-performance-806195dc93d5)

* * *

*本文最初发布于[中](https://medium.com/@kataras/go-vs-net-core-in-terms-of-http-performance-7535a61b67b8)T3*