# 使用 Serilog、LibLog 和 Seq 记录日志

> 原文：<https://dev.to/tonytalkstech/logging-with-serilog-liblog-and-seq-20h>

在我的职业生涯中，我已经编写和重写了大约 300 次日志提供程序。这是我知道怎么做，我知道如何延伸，我知道如何讨厌它。每次我写它的时候，我都希望有一个伟大的抽象层，允许我在未来的应用程序中重用这个库。我不可避免地会失败。

解决我所有问题的方法来了。

# 一些历史

关于我个人观点的一点背景故事:如果一个日志框架真的非常好的话，我对这个日志框架有很强的依赖性是没有问题的。有些人不同意那个观点，没关系。

为此，这篇博文的第一篇是“用 Serilog 和 Seq 记录日志”，主要是因为我太喜欢 Serilog 了。对我来说，它是完美的日志框架，因为它以我大脑工作的方式完成了日志记录。此外，让它发送到 Seq 是显而易见的。能够以一种正常的、以开发人员为中心的方式解构结构日志实在是太好了，不能错过。

然而，每隔一段时间，就会有一种产品出现，彻底改变你做事的方式。

# 玩家 3 已经加入游戏

正当我认为我永远不会改变我在任何应用程序中记录任何东西的方式时，出现了 [LibLog](https://github.com/damianh/LibLog) 。LibLog 是一个看似简单的日志抽象，它使用一些偷偷摸摸的反射来透明地支持特定的日志库(例如 Serilog)。

LibLog 声明的目标是为几个日志程序提供透明的日志支持，特别是主要为*库*开发者。在一个简单的例子中，让我们假设存在一个每个人都使用的库(Newtonsoft。Json，又名 JSON.Net，就是一个很好的例子)。如果 JSON.Net 将 LibLog 包含在它的库中，然后利用 LibLog 提供的日志接口，任何使用 JSON.Net 的应用程序，只要实现了像 Serilog 这样的日志框架，就会立即获得 JSON。Net 的日志也是免费的。

这是一个非常强大的特性，它允许库开发人员不必考虑消费应用程序将使用什么类型的日志框架。

# 我如何使用 LibLog

不幸的是，对于我所做的工作来说，创建供其他应用程序使用的库是不现实的。这不是我通常做的事情。我倾向于开发应用程序本身。这并不意味着我不能使用 LibLog！事实上，我没有直接调用 Serilog，而是设置了 Serilog，然后到处使用 LibLog 的`ILog`接口。这允许我在将来改变日志框架(不管这可能多么不现实)。下面是一些代码示例。

## 记录设置

```
namespace LoggingTest
{
    using Serilog;
    using Serilog.Core;
    using Serilog.Events;
    using ServiceStack.Configuration;

    public class Global : HttpApplication
    {
        private readonly IAppSettings settings = new AppSettings();

        void Application_Start(object sender, EventArgs e)
        {
            this.SetupLogging();
        }

        private void SetupLogging()
        {
            var level = new LoggingLevelSwitch(this.settings.Get("SeqLevel", LogEventLevel.Information));
            Log.Logger = new LoggingConfiguration()
                .WriteTo.Seq(this.settings.Get("SeqUrl", "http://localhost:5341"))
                .Enrich.WithProperty("Application", this.settings.Get("ApplicationName", "LoggingTest"))
                .Enrich.WithProperty("ApplicationVersion", this.settings.Get("ApplicationVersion", "1.0.0"))
                .MinimumLevel.ControlledBy(level)
                .CreateLogger();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 家庭控制器

```
namespace LoggingTest
{
    using LoggingTest.Logging;

    public class HomeController : Controller
    {
        private readonly ILog log = LogProvider.GetCurrentClassLogger();

        public ActionResult Home()
        {
            this.log.InfoFormat("{MethodName} Entry", nameof(Home));
            var stopwatch = Stopwatch.StartNew();
            // do lots of work
            stopwatch.Stop();
            this.log.InfoFormat("{MethodName} Exit | {ElapsedMilliseconds} ms", nameof(Home), stopwatch.ElapsedMilliseconds);
            return this.View();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码示例包含一个设置 Serilog 的`Global`类，利用许多内置的丰富器在每条消息中包含更多信息。注意，我还利用了 [ServiceStack](https://servicestack.net) 的精彩的`IAppSettings`应用程序设置抽象来做大量的检索和转换数据到正确类型的跑腿工作。

后来，我们有了一个 ASP.NET MVC 控制器。在这个控制器中，我们用静态方法`LogProvider.GetCurrentClassLogger()`实例化了 LibLog 的`ILog`接口。这个方法给消息添加了一个`Context`属性，给出了当前的类。

如果您习惯使用 Serilog，您会注意到我们将一个模板化的字符串传递给日志，然后使用`ILog.InfoFormat`方法添加参数。这透明地将结构化消息及其参数传递给 Serilog 的`Log.Info`方法(我已经对此进行了极大的简化)。

# 包装完毕

我已经在我构建的每个新应用程序中使用了 Serilog 和 Seq，无论是个人应用还是专业应用。随着 LibLog 的引入，我能够用一个简单的日志接口来分离我的日志提供者。此外，在我成为某个伟大的库开发人员的不太可能的情况下，我将利用我的任何未来应用程序都将透明支持的工具。总而言之，LibLog 是最好的。

# 参考文献

LibLog:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[【damianah】](https://github.com/damianh)/[【liblog】](https://github.com/damianh/LibLog)

### LibLog 是一个单独的文件，您可以通过 nuget 将其复制/粘贴或安装到您的库/框架/应用程序中，以提供日志抽象。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Image](img/f4f3e858793e4acce4e2f29fb9559d67.png)T2】](https://github.com/damianh/LibLogdocs/liblog_icon.png)

# LibLog [![Build status](img/9ebb99cd6e5c08427b9bda70cf3bc3ce.png) ](https://ci.appveyor.com/project/damianh/liblog) [ ![NuGet Badge](img/896003ba44adf055c5d78eac342be3a9.png)](https://www.nuget.org/packages/LibLog/)

⚠️ LibLog 现已被弃用(见 [#270](https://github.com/damianh/LibLog/issues/270) )。

我建议库开发人员使用 [`Microsoft.Extensions.Logging.Abstractions`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions) ,因为它现在是。网络生态系统。对于希望继续使用 LibLog 或者需要根据需要进行调整的库开发人员来说，应该将代码复制到他们的项目中(毕竟，这正是 LibLog 所做的)。

感谢所有贡献者和用户多年来的支持。

达米安

* * *

专门为**库**开发者设计的，`LibLog`是一个源代码包，你[通过 nuget](https://www.nuget.org/packages/LibLog) 把它安装到你的库/框架/应用中，以提供一个日志抽象。它还包含对 [NLog](http://nlog-project.org/) 、 [Log4Net](https://logging.apache.org/log4net/) 、 [Serilog](http://serilog.net/) 和[放大镜](http://www.gibraltarsoftware.com/Loupe)的透明内置支持，并允许您的用户在必要时定义自定义提供程序。

请参见 [Wiki](https://github.com/damianh/LibLog/wiki) 了解更多信息。

## 旧 csproj 格式的可用性

LibLog 使用`.pp`文件进行名称空间转换(又名…

</article>

[View on GitHub](https://github.com/damianh/LibLog)