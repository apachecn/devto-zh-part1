# 控制台应用程序中的 ServiceStack

> 原文：<https://dev.to/tonytalkstech/servicestack-in-a-console-application>

我绝对**爱** [ServiceStack](https://servicestack.net/) 。让我们开诚布公吧。对于我开始的每个新项目，我的第一个问题是“我如何在这里使用 ServiceStack？”

我使用 ServiceStack 的典型理由是利用它惊人的 web 服务框架。我可以一页一页地赞美它基于消息的设计和可扩展性。

当我在开发 ASP.NET MVC web 应用程序时，我仍然会引入 ServiceStack 来获得它额外的 [MVC 特性](http://docs.servicestack.net/mvc-integration)。这允许我使用内置的 ServiceStack 组件(例如，会话、缓存、身份验证等。)在我最喜欢的 web 应用程序框架之上。

然而，由于我的工作性质，我正在处理控制台应用程序，这些应用程序必须定期从 Windows 任务计划程序中运行。虽然 ServiceStack 很好地与基于 HTTP 的 web 服务和 web 应用程序框架挂钩，但它在纯控制台应用程序中似乎没有太多用处。

如果你认为 ServiceStack 仅仅是一个 web 框架的 T2，那么这几乎是正确的。我个人喜欢将 ServiceStack 视为一个 web 框架和一组工具，我想我会在下辈子构建它们。这些工具包括内置的 Funq IoC 容器和`AppSettings`抽象。下面是我如何在一个真实项目中做到这一点的例子。

## 使用 Funq

Funq 是 ServiceStack 内置的 IoC 容器。其他令人惊叹的 IoC 容器也存在(我个人喜欢 [Autofac](https://autofac.org/) )，但我更喜欢这个内置的。

在典型的 web 应用程序中，Funq 容器是在每个作用域中正确创建的，并由 ServiceStack 管理。在控制台应用程序中，这种级别的集成不起作用，所以我通过创建一个对整个应用程序可用的静态容器来解决这个问题。这有点像服务定位器模式，但是我们并没有按照惯例走这条路线**。请注意，从技术上来说，阻止开发人员直接调用容器而不是利用构造函数注入没有任何问题，但是我们会对这样做的开发人员大吼大叫。**

下面的代码显示了一个示例控制台应用程序，它有一个由`DependencyConfig`类使用的静态`Container`。由此，接口的每个实现都利用构造函数注入来获得依赖项的实现。

### Program.cs

```
namespace ServiceStack.Console
{
    static class Program
    {
        public static readonly Container Container = new Container();

        static void Main(string[] args)
        {
            DependencyConfig.Setup();
            new LogConfig(Container.Resolve<IAppSettings>()).Setup();

            var worker = new Worker(Container.Resolve<IWorkManager>());
            worker.Work();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】dependency config . cs](#dependencyconfigcs)

```
namespace ServiceStack.Console
{
    public static class DependencyConfig
    {
        public static void Setup()
        {
            Program.Container.Register<IAppSettings>(x => new AppSettings());
            Program.Container.Register<IDbConnectionFactory>(x =>
            {
                var settings = x.Resolve<IAppSettings>();
                var dbFactory = new OrmLiteConnectionFactory(settings.Get("ConnectionString", "default connection string"), new OracleOrmLiteDialectProvider(false, false, OracleOrmLiteDialectProvider.ManagedProvider));
                return dbFactory;
            });

            Program.Container.Register<IWorkRepository>(x => new WorkRepository(x.Resolve<IAppSettings>(), x.Resolve<IDbConnectionFactory>()));
            Program.Container.Register<IWorkManager>(x => new WorkManager(x.Resolve<IWorkRepository>()));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### Worker.cs

```
namespace ServiceStack.Console
{
    public class Worker
    {
        private readonly IWorkManager manager;

        public Worker(IWorkManager manager)
        {
            this.manager = manager;
        }

        public void Work()
        {
            this.manager.DoWork();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### IWorkManager.cs

```
namespace ServiceStack.Console.Interfaces
{
    public interface IWorkManager
    {
        void DoWork();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### WorkManager.cs

```
namespace ServiceStack.Console
{
    public class WorkManager : IWorkManager
    {
        private readonly IWorkRepository repository;

        public WorkManager(IWorkRepository repository)
        {
            this.repository = repository;
        }

        public void DoWork()
        {
            var data = this.repository.GetData();
            DoTheThing(data);
        }

        private void DoTheThing(object data)
        {
            // Data
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### IWorkRepository.cs

```
namespace ServiceStack.Console.Interface
{
    public interface IWorkRepository
    {
        object GetData();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### WorkRepository.cs

```
namespace ServiceStack.Console
{
    public class WorkRepository : IWorkRepository
    {
        private readonly IAppSettings settings;
        private readonly IDbConnectionFactory connectionFactory;

        public WorkRepository(IAppSettings settings, IDbConnectionFactory connectionFactory)
        {
            this.settings = settings;
            this.connectionFactory = connectionFactory;
        }

        public object GetData()
        {
            using (var connection = this.connectionFactory.OpenDbConnection())
            {
                // Get and return data
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种类型的应用程序架构确保了一个简单的单元测试计划，因为每个具体的实现都可以通过使用模拟对象来单独测试(我喜欢使用 [Moq](https://github.com/moq/moq4) 来测试)。

另一点需要注意的是使用`IDbConnectionFactory`接口来访问数据库。这允许我们在测试中轻松地使用内存数据库，比如 Sqlite，以便验证难以测试的数据访问类。

## 我喜欢 AppSettings 抽象

我亲自编写了一个强类型配置抽象来镜像我的`web.config`或`app.config`中的内容至少十几次。事实上，[我甚至在博客上写了这件事](https://tonytalks.technology/asp-net-and-azure-cloud-services-5a6f517dce78#.j8b31yl0p)。我已经写完了这种类型的紧耦合，因为它使得更新有点太麻烦了。

作为替代，我现在保留了一个定义了`<appSettings />`键的类，以及一个包含这些键的默认值的类。

### ConfigKeys.cs

```
namespace ServiceStack.Console
{
    public static class ConfigKeys
    {
        public static readonly string ApiKey = nameof(ApiKey);
        public static readonly string ApplicationName = nameof(ApplicationName);
        public static readonly string ApplicationVersion = nameof(ApplicationVersion);
        public static readonly string Environment = nameof(Environment);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### ConfigValues.cs

```
namespace ServiceStack.Console
{
    public static class ConfigValues
    {
        public static readonly string DefaultApiKey = "abcd1234";
        public static readonly string DefaultApplicationName = "ServiceStack Console Test Application";
        public static readonly string DefaultApplicationVersion = "1.0.0";
        public static readonly string DefaultEnvironment = "Local";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的构造函数注入和 Funq，然后我通过使用`IAppSettings.Get<T>(string name, T defaultValue)`方法从`IAppSettings`实例中提取数据。一个很好的例子是当我在上面的`DependencyConfig`类中检索`"ConnectionString"`值时。

## 包起来

我在上面输入的内容都不依赖于 ServiceStack。我可以引入 Autofac 并使用无数的`web.config`或`app.config`抽象库中的任何一个(或者使用我自己的),代码将完全一样地工作。然而，由于我对 ServiceStack 的经验水平(老实说，还有它的易用性)，我倾向于使用我所知道的。在这种情况下，这意味着引入一个 web 框架来利用它的 IoC 容器和应用程序配置抽象。