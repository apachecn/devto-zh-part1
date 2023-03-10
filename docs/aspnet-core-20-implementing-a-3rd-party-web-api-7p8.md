# ASP.NET 核心 2.0:实现第三方 Web API

> 原文：<https://dev.to/donbavand/aspnet-core-20-implementing-a-3rd-party-web-api-7p8>

##### 这篇文章最初发表在我的[博客网站](http://danieldonbavand.com/implementing-an-api/)

# 简介

开发人员在使用或构建应用程序时的一项常见任务是实现 API 并与之交互。

在这篇文章中，我们将看看什么是 API。然后我们将继续创造我们自己的。实现第三方 Web API 的 NET Core 2.0 Web API。

# 什么是第三方 API

API 是应用程序编程接口的首字母缩写，它是一个软件中介，允许两个应用程序相互通信。

有许多不同类型的 API，其中一个比较常见的类型是 Web API，它们通常被称为 Web 服务。它们为 Web 应用程序提供了一个接口，并允许应用程序通过 internet 相互连接和通信。您可以创建自己的 API 供他人交互，也可以与他人的 API 交互

有数以千计的公共 API 可以使用，这些 API 可以检查天气更新，获取和确认地址细节，进行在线支付和更新社交媒体状态。

一个应用编程接口的例子，如果你想到你访问过的任何一个网站，在那里你被要求填写你的地址细节，你首先输入你的街道号码和名字，然后地址列表被显示，你选择一个地址，其余的字段被填充。该网站可能使用第三方 Web API 调用地址服务来获取符合搜索条件的已知地址列表。

虽然有许多不同类型的 API，这篇文章将主要关注 Web APIs。

我们要实现的例子是一个 Giphy API[https://giphy.com/](https://giphy.com/)。Giphy 是一个存储 GIF 图像的网站，当用户输入时，Giphy 提供特定的搜索标准，Giphy 会尝试将其与图像匹配。这个 API 允许我们发送一个 Http 请求并接收一个 GIF 图像 url。

# 我们将要覆盖的内容

*   创建一个简单的 ASP.NET 核心 2.0 Web API 项目
    *   如果您不确定如何创建空白。NET Core 2.0 项目，然后访问我的 YouTube 频道，在那里我向你展示这是如何做的[ASP.NET Core 2.0:构建一个简单的 Web API](https://youtu.be/lv43vrCo2fg)
*   搜索并阅读 API (Giphy)文档
*   创建我们的控制器
*   依赖注入
*   项目结构
*   创建模型
*   创建服务类别
*   创建 GetRandomGif 类
*   运行应用程序

# 实现 GIPHY API

### 创建一个空的。NET Core 2.0 Web API 项目

为了开始实现我们的 Giphy API，我们需要创建一个空白。NET 核心 Web API 项目。

我在 YouTube 上创建了一个教程[ASP.NET 核心 2.0:构建一个简单的网络应用编程接口](https://youtu.be/lv43vrCo2fg)来演示如何创建这个。如果您在创建 Web API 时需要帮助，那么在继续下一步之前，请遵循该教程。

### 阅读 API 文档

在我们开始实现任何东西之前，确保我们已经阅读了 API 文档是非常重要的。API 文档包含关于如何有效使用和集成 API 的说明。

好的 API 文档会告诉你使用限制、请求需要什么样的输入、请求的输出是什么样子以及其他实现细节。

不幸的是，并不是所有的 API 都有好的文档，你需要利用你所得到的，有时你需要做一些探索性的学习。

通过阅读 API 文档，你可以使用你最喜欢的 http 请求客户端调用 API 并查看结果，目前，我使用的是 PostMan。通过确保你提出的请求返回给你你所期望的结果，意味着你已经明白提出请求需要什么。这也将使我们的解决方案更容易实现。

我们将使用的网址是[http://api.giphy.com/v1/gifs/search?api_key={giphyKey}&q = { searchCritera }&limit = 1](http://api.giphy.com/v1/gifs/search?api_key=%7BgiphyKey%7D&q=%7BsearchCritera%7D&limit=1)

#### Giphy 链接

网址:[https://giphy.com/](https://giphy.com/)
开发者网址:[https://developers.giphy.com/](https://developers.giphy.com/)T5】Giphy 文档:[https://developers.giphy.com/docs/](https://developers.giphy.com/docs/)

### 创建我们的控制器

首先，我们添加一个返回 OK 的控制器。

然后我们添加 Route[Route(" v1/gi phy/random/{ search critera } ")，这将允许我们到达端点并传入一个值，该值将是我们想要搜索的内容。

```
[Route("v1/giphy")]
    public class GiphyController : Controller
    {
        [HttpGet]
        [Route("random/{searchCritera}")]
        public IActionResult GetRandomGif(string searchCritera)
        {
            return Ok();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们对控制器和方法有了基本的概念，我们将会遵循一种叫做希望编程的编码风格。

一厢情愿的编程基本上意味着我们在编写代码时就已经有了可用的类和方法，即使我们还没有创建它们。然后我们在需要的时候创建这些类和方法。

让我们试试那个。我们知道控制器将调用一个 GiphyServices 类，所以我们可以在控制器中编写以下代码。

```
 [HttpGet]
        [Route("v1/giphy/random/{searchCritera}")]
        public async Task<IActionResult> GetRandomGif(string searchCritera)
        {
            var result = await _giphyServices.GetRandomGifBasedOnSearchCritera(searchCritera);

            return Ok(result);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**在这段代码中，你会看到我将使用 async 和 await 关键字。C#中的异步编程需要一个完整的博客帖子。因此，我不会详细讨论它在做什么。如果同时你想了解更多关于异步编程的知识，那么你可以在这里[阅读更多。](https://docs.microsoft.com/en-us/dotnet/csharp/async)

## 依赖注入

我们将使用 ASP.NET 核心内置的依赖注入框架。不久前我写了一篇博文，解释了一些关于依赖倒置、注入和控制的内容，你可以在这里阅读

ASP.NET 核心中的依赖注入介绍 ASP.NET 核心[MSDN 也有一篇很好的文章](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)

依赖注入的一个非常简单的解释是，为了访问和使用类中的方法，我们需要实例化类，我们可以通过更新类来做到这一点，即 var x = new GiphyServices()；或者使类成为静态的。

我们将如何在 Giphy 项目中使用依赖注入，这将允许我们在启动时实例化我们的类，然后将它们注入到每个需要它们的类的构造函数中。

请记住，这是我们为什么使用依赖注入的一个非常简单的解释，它还有许多其他用途。

为了使用和实现依赖注入，我们需要一个类和一个用于我们的类的接口，即 IGiphyServices 和 GiphyServices，然后我们将下面的代码添加到 startup.cs 的 ConfigureServices 方法下

```
 public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton<IGiphyServices, GiphyServices>();
        } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以将它注入到我们的 GiphyController 类构造函数

```
 private readonly IGiphyServices _giphyServices;
        public GiphyController(IGiphyServices giphyServices)
        {
            _giphyServices = giphyServices;
        } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以调用 GiphyServices 类了。

```
 [HttpGet]
        [Route("v1/giphy/random/{searchCritera}")]
        public IActionResult GetRandomGif(string searchCritera)
        {
            var result = _giphyServices.GetRandomGifBasedOnSearchCritera(searchCritera);
            return Ok();
        } 
```

Enter fullscreen mode Exit fullscreen mode

### 创建项目结构

在设计和构建应用程序时，重要的是我们要花时间思考如何设计我们的应用程序，以及我们应该把所有东西放在哪里。

我们的 Giphy 解决方案将包含两个项目，一个 Giphy。API，我们将在其中存储我们的控制器和一个 Giphy.Libs。

在 Libs 项目中，我们将创建一个服务类，它将作为调用 Libs 项目中的 Giphy 类的粘合剂，一旦我们收到结果，我们将把它发送回控制器。

然后我们将创建一个 Giphy 类，这将用于实现 Giphy API。然后，我们将点击 Giphy 端点并获取一个 gif 图像 URL。

我们还需要在 Libs 项目中创建一个模型类，这个模型将包含属性。一旦我们从 Giphy API 收到结果，我们就需要将结果反序列化到我们的模型中。该模型将保存 GIF URL，然后我们将把它发送回发出请求的人。

下面是该结构的图像

[https://thepractical dev . S3 . Amazon AWS . com/I/euf 9 rbh 5 tjlpfbetrfdr . png](https://thepracticaldev.s3.amazonaws.com/i/euf9rbh5tjlpfbetrfdr.png)

### 创建模型

Giphy 项目中的模型是一个类文件，它有一个或多个属性，用于存储从 Giphy API 返回的结果。

然后，我们会将模型中的结果返回给发出请求的用户。

在上面的阅读 API 文档一节中，我们找到了向 Giphy 发出请求所需的端点。该请求应该返回给我们一个类似如下的 JSON 对象。

[https://thepractical dev . S3 . Amazon AWS . com/I/rz2uscdvokenm 4 p 73 nbq . png](https://thepracticaldev.s3.amazonaws.com/i/rz2uscdvokenm4p73nbq.png)

我们可以从这个结果中计算出，我们希望模型中包含什么。虽然这里返回了大量数据，但我们通常并不需要显示的所有内容。在这种情况下，我们将只采用 bitly_gif_url 属性。

当创建一个直接映射到由 API 返回的 json 结果的模型时，我们希望遵循它的结构，即我们创建两个类，一个包含一个属性名为 data 的 IEnumerable(集合),另一个包含一个属性名为 bitly_gif_url 的字符串。我们这样做是为了将 json 对象反序列化到模型中。更多即将推出…

**吉非摩德尔**

```
public class GiphyModel
    {
        public IEnumerable<data> data { get; set; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

**数据模型**

```
public class data
    {
        public string bitly_gif_url { get; set; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 创建服务类

在 services 文件夹中，我们现在要创建一个名为 Giphy services 的类以及一个匹配的接口。这将是从控制器获取请求并将其传递给 Giphy 类的粘合剂，一旦我们收到结果，我们就将其传递回控制器。

注意，我们的返回类型是之前创建的 GiphyModel。这是我们将返回给控制器的内容。

使用一厢情愿的编程，我们调用下一个类中的方法，我们将创建' GetRandomGif'

```
public class GiphyServices : IGiphyServices
    {
        private static IGetRandomGif _getRandomGif;

        public GiphyServices(IGetRandomGif getRandomGif)
        {
            _getRandomGif = getRandomGif;
        }

        public async Task<GiphyModel> GetRandomGifBasedOnSearchCritera(string searchCritera)
        {
            return await _getRandomGif.ReturnRandomGifBasedOnTag(searchCritera);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 GetRandomGif 类

这个类将调用 Giphy API 端点并返回一个结果，我们还将使用 newtonsoft json 反序列化将结果反序列化到我们的 GiphyModel 中。

让我们继续创建一个 GetRandomGif 类和一个 IGetRandomGif 接口，这样我们就可以依赖注入这个类并在 GiphyServices 类中使用它。

```
public class GiphyServices : IGiphyServices
    {
        private readonly IGetRandomGif _getRandomGif;
        public GiphyServices(IGetRandomGif getRandomGif)
        {
            _getRandomGif = getRandomGif;
        }
        public async Task<GiphyModel> GetRandomGifBasedOnSearchCritera(string searchCritera)
        {
            return await _getRandomGif.ReturnRandomGifBasedOnTag(searchCritera);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们需要确保已经将 GetRandomGif 类添加到 startup.cs 类中的 ConfigureServices 方法中。

```
 public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton<IGiphyServices, GiphyServices>();
            services.AddSingleton<IGetRandomGif, GetRandomGif>();
        } 
```

Enter fullscreen mode Exit fullscreen mode

您需要安装以下 nuget 包“Newtonsoft。JSON"
到您的 Giphy 中。Libs 项目

```
 install-package Newtonsoft.JSON 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 HttpClient 向 Giphy API 端点发出请求，一旦得到响应，我们就将 JSON 对象反序列化到我们之前创建的 Giphy 模型中。

```
public class GetRandomGif : IGetRandomGif
    {
        public async Task<GiphyModel> ReturnRandomGifBasedOnTag(string searchCritera)
        {
            const string giphyKey = "";
            using (var client = new HttpClient())
            {
                var url = new Uri($"http://api.giphy.com/v1/gifs/search?api_key={giphyKey}&q={searchCritera}&limit=1");
                var response = await client.GetAsync(url);
                string json;
                using (var content = response.Content)
                {
                    json = await content.ReadAsStringAsync();
                }
                return JsonConvert.DeserializeObject<GiphyModel>(json);
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**您需要将自己的 Giphy API 密钥添加到上面的 giphyKey 常量中。

```
const string giphyKey = ""; 
```

Enter fullscreen mode Exit fullscreen mode

### 运行应用程序

如果我们运行应用程序，我们应该能够到达我们的终点

```
{localhost}/v1/giphy/random/{searchCritera} 
```

Enter fullscreen mode Exit fullscreen mode

[https://thepractical dev . S3 . Amazon AWS . com/I/nfxs 3 ngawzyivw 2 PC 48 e . png](https://thepracticaldev.s3.amazonaws.com/i/nfxs3ngawzyivw2pc48e.png)

[https://thepractical dev . S3 . Amazon AWS . com/I/4n 491 guf 8 cjp 4hz 7 rvfl . png](https://thepracticaldev.s3.amazonaws.com/i/4n491guf8cjp4hz7rvfl.png)

### 总结

在这篇博文中，我们研究了什么是 API，以及它们是如何在任何地方使用的。许多公司将 Web APIs 作为服务提供，这允许我们从他们提供的服务中进行交互和收集信息。

在上面的例子中，Giphy 提供了一个 gif 图像服务，你输入一个搜索词，它会返回一个与你的搜索相关的 GIF 的 URL。

在编程中，我们总是与 Web APIs 交互，通过学习如何找到和阅读 API 的文档，然后与它交互，这是一项非常有用的编码技能。

记住，阅读文档以确保你理解你正在实现的东西是非常重要的。

你可以在我的 gitHub repo
[https://github.com/donbavand/Giphy](https://github.com/donbavand/Giphy)中找到我们上面构建的工作代码

我在 YouTube 上做了一个录像，你可以在这里看。

如果你有任何问题、意见或任何不合理的地方，请通过 twitter [@donbavand](https://twitter.com/Donbavand) 联系我，我会非常乐意帮助你。

我也有一个 [YouTube 频道](https://www.youtube.com/user/MultiB0x),我正在制作操作指南，你可以订阅。