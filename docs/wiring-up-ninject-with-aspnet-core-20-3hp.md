# 用 ASP.NET Core 2.0 连接 Ninject

> 原文：<https://dev.to/cwetanow/wiring-up-ninject-with-aspnet-core-20-3hp>

*本指南适用于 Ninject 版本 3.3.x*

[Ninject](http://www.ninject.org/) 是一个闪电般快速、超轻量的依赖注入器，用于。NET 应用程序。它帮助您将应用程序分割成一系列松散耦合、高度内聚的部分，然后以灵活的方式将它们粘合在一起。通过使用 Ninject 来支持您的软件架构，您的代码将变得更容易编写、重用、测试和修改。

为了测试注入器是否正常工作，创建一个实现接口
的服务

```
 public interface ITestService
    {
        string GetData();
    }

    public class TestService : ITestService
    {
        public string GetData()
        {
            return "some magic string";
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

#### 从 Nuget 下载软件包

使用软件包管理器控制台`Install-Package Ninject -Version 3.3.4`

使用点网命令行界面`dotnet add package Ninject --version 3.3.4`

#### 将这些成员添加到`Startup.cs`类，如下所示

```
 private readonly AsyncLocal<Scope> scopeProvider = new AsyncLocal<Scope>();
    private IKernel Kernel { get; set; }

    private object Resolve(Type type) => Kernel.Get(type);
    private object RequestScope(IContext context) => scopeProvider.Value;  

    private sealed class Scope : DisposableObject { } 
```

Enter fullscreen mode Exit fullscreen mode

#### 在`ConfigureServices` ( `Startup.cs`)的末尾增加以下绑定

`services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();`

#### 创建类`RequestScopingStartupFilter`实现`IStartupFilter`接口

```
 public sealed class RequestScopingStartupFilter : IStartupFilter
    {
        private readonly Func<IDisposable> requestScopeProvider;

        public RequestScopingStartupFilter(Func<IDisposable> requestScopeProvider)
        {
            if (requestScopeProvider == null)
            {
                throw new ArgumentNullException(nameof(requestScopeProvider));
            }

            this.requestScopeProvider = requestScopeProvider;
        }

        public Action<IApplicationBuilder> Configure(Action<IApplicationBuilder> nextFilter)
        {
            return builder =>
            {
                ConfigureRequestScoping(builder);

                nextFilter(builder);
            };
        }

        private void ConfigureRequestScoping(IApplicationBuilder builder)
        {
            builder.Use(async (context, next) =>
            {
                using (var scope = this.requestScopeProvider())
                {
                    await next();
                }
            });
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

#### 用下面的扩展方法创建一个静态类`AspNetCoreExtensions`

```
 using System;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Extensions.DependencyInjection;

    public static class AspNetCoreExtensions
    {
        public static void AddRequestScopingMiddleware(this IServiceCollection services, 
            Func<IDisposable> requestScopeProvider)
        {
            if (services == null)
            {
                throw new ArgumentNullException(nameof(services));
            }

            if (requestScopeProvider == null)
            {
                throw new ArgumentNullException(nameof(requestScopeProvider));
            }

            services
                .AddSingleton<IStartupFilter>(new
                    RequestScopingStartupFilter(requestScopeProvider));
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置使用`ConfigureServices`中的中间件(在方法的最后)

`services.AddRequestScopingMiddleware(() => scopeProvider.Value = new Scope());`

#### 用以下内容创建一个文件`Activators.cs`

```
 public sealed class DelegatingControllerActivator : IControllerActivator
        {
            private readonly Func<ControllerContext, object> controllerCreator;
            private readonly Action<ControllerContext, object> controllerReleaser;

            public DelegatingControllerActivator(Func<ControllerContext, object> controllerCreator,
                Action<ControllerContext, object> controllerReleaser = null)
            {
                this.controllerCreator = controllerCreator ?? 
                    throw new ArgumentNullException(nameof(controllerCreator));
                this.controllerReleaser = controllerReleaser ?? ((_, __) => { });
            }

            public object Create(ControllerContext context) => this.controllerCreator(context);
            public void Release(ControllerContext context, object controller) =>             
                this.controllerReleaser(context, controller);
        } 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加以下扩展方法到`AspNetCoreExtensions.cs`

```
 public static void AddCustomControllerActivation(this IServiceCollection services,
            Func<Type, object> activator)
        {
            if (services == null) throw new ArgumentNullException(nameof(services));
            if (activator == null) throw new ArgumentNullException(nameof(activator));

            services.AddSingleton<IControllerActivator>(new DelegatingControllerActivator(
                context => activator(context.ActionDescriptor.ControllerTypeInfo.AsType())));
        } 
```

Enter fullscreen mode Exit fullscreen mode

#### 在`ConfigureServices`的末尾添加以下内容

`services.AddCustomControllerActivation(Resolve);`

#### 添加另一个类到`Activators.cs`

```
 public sealed class DelegatingViewComponentActivator : IViewComponentActivator
        {
            private readonly Func<Type, object> viewComponentCreator;
            private readonly Action<object> viewComponentReleaser;

            public DelegatingViewComponentActivator(Func<Type, object> viewComponentCreator,
                Action<object> viewComponentReleaser = null)
            {
                this.viewComponentCreator = viewComponentCreator ?? 
                    throw new ArgumentNullException(nameof(viewComponentCreator));
                this.viewComponentReleaser = viewComponentReleaser ?? (_ => { });
            }

            public object Create(ViewComponentContext context) =>
                this.viewComponentCreator(context.ViewComponentDescriptor.TypeInfo.AsType());

            public void Release(ViewComponentContext context, object viewComponent) =>
                this.viewComponentReleaser(viewComponent);
        } 
```

Enter fullscreen mode Exit fullscreen mode

#### 和`AspNetCoreExtensions.cs`中的另一种扩展方法

```
 public static void AddCustomViewComponentActivation(this IServiceCollection services, 
            Func<Type, object> activator)
        {
            if (services == null) throw new ArgumentNullException(nameof(services));
            if (activator == null) throw new ArgumentNullException(nameof(activator));

            services.AddSingleton<IViewComponentActivator>(
new DelegatingViewComponentActivator(activator));
        } 
```

Enter fullscreen mode Exit fullscreen mode

然后调用表单`ConfigureServices`(应该是最后调用的)

这是`ConfigureServices`现在应该的样子

```
 public void ConfigureServices(IServiceCollection services)
        {
            // Other configurations

            services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();

            services.AddRequestScopingMiddleware(() => scopeProvider.Value = new Scope());
            services.AddCustomControllerActivation(Resolve);
            services.AddCustomViewComponentActivation(Resolve);

        } 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建一个包含静态类的`ApplicationBuilderExtensions.cs`

```
 using System;
    using System.Globalization;
    using System.Linq;
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc.ApplicationParts;
    using Microsoft.AspNetCore.Mvc.Controllers;
    using Microsoft.Extensions.DependencyInjection;
    using Ninject;

    public static class ApplicationBuilderExtensions
    {
        public static void BindToMethod<T>(this IKernel config, Func<T> method)
 => config.Bind<T>().ToMethod(c => method());

        public static Type[] GetControllerTypes(this IApplicationBuilder builder)
        {
            var manager = builder.ApplicationServices.GetRequiredService<ApplicationPartManager>();

            var feature = new ControllerFeature();
            manager.PopulateFeature(feature);

            return feature.Controllers.Select(t => t.AsType()).ToArray();
        }

        public static T GetRequestService<T>(this IApplicationBuilder builder) where T : class
        {
            if (builder == null) throw new ArgumentNullException(nameof(builder));

            return GetRequestServiceProvider(builder).GetService<T>();
        }

        private static IServiceProvider GetRequestServiceProvider(IApplicationBuilder builder)
        {
            var accessor = builder.ApplicationServices.GetService<IHttpContextAccessor>();

            if (accessor == null)
            {
                throw new InvalidOperationException(      
          typeof(IHttpContextAccessor).FullName);
            }

            var context = accessor.HttpContext;

            if (context == null)
            {
                throw new InvalidOperationException("No HttpContext.");
            }

            return context.RequestServices;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在`Startup`类
中添加以下方法

```
 private IKernel RegisterApplicationComponents(IApplicationBuilder app)
        {
            // IKernelConfiguration config = new KernelConfiguration();
            var kernel = new StandardKernel();

            // Register application services
            foreach (var ctrlType in app.GetControllerTypes())
            {
                kernel.Bind(ctrlType).ToSelf().InScope(RequestScope);
            }

            // This is where our bindings are configurated
             kernel.Bind<ITestService>().To<TestService>().InScope(RequestScope);            

            // Cross-wire required framework services
            kernel.BindToMethod(app.GetRequestService<IViewBufferScope>);

            return kernel;
        } 
```

Enter fullscreen mode Exit fullscreen mode

并从`Configure`(开头)开始调用

`this.Kernel = this.RegisterApplicationComponents(app);`

#### 创造一个`TestController`看看我们的 DI 是否管用

```
 [Route("api/[controller]")]
    public class ValuesController : Controller
    {
        private readonly ITestService testService;

        public ValuesController(ITestService testService)
        {
            this.testService = testService;
            this.factory = factory;
        }

        [HttpGet]
        public IActionResult Get()
        {
            var result = this.testService.GetData();

            return this.Ok(result);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`TestService`的构造函数和`Get`动作中放置一个断点来看看神奇之处。

#### 我在这个[网虫](https://github.com/dotnetjunkie/Missing-Core-DI-Extensions)回购的一点帮助下，根据[在 stackoverflow 上的这个](https://stackoverflow.com/questions/46693305/how-to-integrate-ninject-into-asp-net-core-2-0-web-applications)回答整理了这篇文章。