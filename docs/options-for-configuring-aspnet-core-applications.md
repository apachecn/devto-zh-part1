# 配置 ASP.NET 核心应用程序的选项

> 原文：<https://dev.to/rionmonster/options-for-configuring-aspnet-core-applications>

*这篇文章最初发表在[我的博客](http://rion.io)上。*

关于项目配置的所有争议。NET Core 中，有一件事已经成功地保持了基于 JSON 的一致性，那就是在这个新世界中应用程序设置配置文件的使用。虽然 JSON 的*读*非常容易，但是如果您存储的是由嵌套对象和数组组成的大型复杂对象，每个对象都有不同类型的多个属性，等等，那么 JSON 很快就会变得混乱。

谢天谢地，你还有选择。我不是指像使用 XML 这样的选择，我是指有一个专门的框架来处理这样的问题，并使这些潜在的繁琐配置更加易于管理。

## 进入选项框架

[Options framework](https://github.com/aspnet/Options) 是一个非常基本的框架，专门用于访问和配置 POCO 设置。NET Core，它简直再容易不过了。

现在，假设您的应用程序有一个如下所示的配置文件:

```
{  "ApplicationLayout":  {  "LayoutChangingEnabled":  true,  "Layouts":  [  {  "Name":  "Standard",  "Modules":  [  {  "Name":  "Foo",  "Order":  1  },  {  "Name":  "Bar",  "Order":  2  }  ]  },  {  "Name":  "Detailed",  "Modules":  [  {  "Name":  "Foo",  "Order":  1  },  {  "Name":  "Bar",  "Order":  2  },  {  "Name":  "Admin",  "Order":  3  }  ]  }  ]  }  } 
```

这相当简单。但是如果您想要访问任何一个单独的项目，那么深入嵌套元素可能会非常麻烦，并且会搞乱您的代码。谢天谢地，这是期权框架真正闪光的地方。

选项将允许您定义一个 C#类来表示您的配置设置，并且它将通过一行代码将您现有的 JSON 配置绑定到这个类。让我们看看上一个示例的结构是什么样的:

```
public class ApplicationConfiguration
{
    public ApplicationLayout Layout { get; set; }            
}

public class ApplicationLayout
{
    public bool LayoutChangingEnabled { get; set; }
    public Layout[] Layouts{ get; set; }
}

public class Layout
{
    public string Name { get; set; }
    public Module[] Modules { get; set; }
}

public class Module
{
    public string Name { get; set; }
    public int Order { get; set; }
} 
```

现在我们需要做的只是在我们的应用程序中包含选项 NuGet 包:

```
Install-Package Microsoft.Extensions.Options 
```

然后在`Startup.cs`文件中，我们可以像预期的那样读取我们的配置文件:

```
var config = new ConfigurationBuilder()
    .AddJsonFile("YourConfigurationFile.json")
    .Build(); 
```

然后简单地连接一个`IOptions`服务，它将允许您的强类型配置作为服务注入到应用程序中的任何地方:

```
public void ConfigureServices(IServiceCollection services)
{
    // Omitted for brevity

    services.AddOptions();
    services.Configure<ApplicationConfiguration>(config);
} 
```

有了所有这些措施，您现在只需在应用程序的不同位置注入您的选项，允许您从配置中干净地访问您需要的数据，而无需向下钻取多个层或嵌套元素:

```
public class FooController
{
     private readonly ApplicationConfiguration _options;

     public FooController(IOptions options)
     {
          // Access your options here 
          var canChangeLayout = options.Layout.LayoutChangingEnabled; // "true"
     }
} 
```

根据您的具体情况，您可能希望覆盖设置中的某个值。这很容易做到，只需要在初始配置后使用一个委托，如下所示:

```
public void ConfigureServices(IServiceCollection services)
{
    // Omitted for brevity

    services.Configure<ApplicationConfiguration>(config);

    // Update the configuration
    services.Configure<ApplicationConfiguration>(options =>
    {
        options.Layout.LayoutChangingEnabled = false;
    });
} 
```

根据应用程序的复杂程度，您可能会发现许多其他更高级的用例很有帮助，例如使用快照来检测配置更改、绑定到对象图以及许多其他功能，您可以在文档中进一步了解这些功能。

## 看他们的行动

ASP.NET 团队和社区的几个成员一起工作，把几乎所有类型的配置场景的例子放到 GitHub 上，如果你想看看它们实际上是怎样的。如果您需要处理一些更高级的用例(例如，使用内存提供者、快照、对象图等),我强烈推荐浏览它们。)在您的应用程序中。