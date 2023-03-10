# 用 Airtable 和 Azure 函数构建一个“重定向器”

> 原文：<https://dev.to/jonstodle/building-a-redirector-with-airtable-and-azure-functions-5kp>

偶尔我想要一个简单的 url 别名。假设我想要指向我的堆栈溢出配置文件的链接，但是我想要一个简单、简短且容易记住的 url。我希望有一种简单的方法来注册组合系统，并指定一个简单而简短的别名 url 来重定向到我的堆栈溢出配置文件。

这就是我们要在这里建造的。

这个应用程序有两个部分。第一部分是存储:我需要在某个地方存储 url 别名。我选择了空中桌。Airtable 是简单的在线表格存储；它就像一个轻量级版本的 Excel。Airtable 也有一个很好的 JSON API 来访问你账户中的表格。

第二部分是代码本身。这基本上只是一个简单的接收请求的处理程序。取域后的路径，在别名表中查找。如果匹配，重定向到指定的 url。对于这一部分，我选择使用 Azure 函数。

使用 Azure Functions，您只需为每次执行该函数付费，但每月的前一百万次执行是免费的(！).这意味着它对我的小用例是免费的。

与 Azure Web 应用程序一样(Azure Functions 只是一个幕后的 Web 应用程序)，一个功能在不使用时会被关闭。与网络应用相比，这个功能启动起来相当快。一个 web 应用程序可能需要 10-20 秒才能启动，但该功能只需 2-5 秒就能响应。这使得用户体验更好，因为他们不必等待重定向那么长时间。

让我们来设置一下:

# 充气表

去[Airtable.com](https://airtable.com/)注册账户。首先我们创建一个*团队*。一个团队可以包含多个用户，这些用户可以访问 Airtable 中的相同资源。我们用它来组织我们的基地。出于演示的目的，我创建了一个名为 blog.jonstodle.com*的团队。*

接下来我们创建一个*基础*。一个库就像一个 Excel 文档，它可以包含多个数据表。点击加号图标，选择*从头开始*。我已经打电话给基地*重定向*。

[![Airtable team](img/573a5fc92177acc2d2b50de8c8756db4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xtLEUgeA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/conteimg/2017/09/Screenshot-170918-104327.png)

点击图标打开底座。右键单击表格选项卡(显示为*表格 1* ，并将其重命名为*路径*。接下来，我们将使用以下类型定义三个列。右键单击现有的列标题，并点击*自定义字段类型*以更改名称和字段类型:

```
thead{
    font-weight: bolder;
} 
```

|内容类型|名称|
|单行文本|路径|
| URL | Url |
|单行文本|描述|

我还添加了一些数据来处理。

[![Redirects table](img/49f561734ccfa9eaf45612972a5f0b86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8bU2RBg9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/conteimg/2017/09/Screenshot-170918-105256.png)

要了解如何通过 API 访问数据，请点击右上角的问号，然后点击 *API 文档*。

[![](img/0cb7dd7864812cea4a5dc1442ad388b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--324tGuqP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/conteimg/2017/09/Screen-Shot-2017-09-19-at-10.15.28.png)

在此页面上，您将能够看到 API 键(通过切换右上角的*显示 API 键*)和 REST 端点，以从 base 获取数据。您将从 API 中获得一些很好的响应示例，其中包含来自实际基础的实时数据。

这里我们需要的重要东西是基本 URL([https://api.airtable.com](https://api.airtable.com))和基本的 id(路径中在 *v0* 之后的部分)。在我的例子中，我的基地的端点是 https://api.airtable.com/v0/appxpeLm4zw7rVbMV 的。

记下 API 键和端点，以备后用。

这就是我们从 Airtable 中需要的一切，所以让我们来看看我们将要使用的 Azure 函数。

# 天蓝色功能

启动 [Azure 门户](https://portal.azure.com/)并点击侧边栏中的加号。在*计算*类别下，你会找到*功能 App* 。填写字段，确保*托管计划*设置为*消费计划*。下面是我怎么填的。

[![Azure Function setup](img/a5e7e21366b90d75cb86bf06665b104b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZCFa5Cnd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/conteimg/2017/09/Screenshot-170918-111244.png)

点击*创建*，Azure 会为你部署一个新的功能 app。部署完成后，你可以在*更多服务* > *功能应用*下的工具条中找到该功能应用。

在列表中选择您的功能应用，您将看到以下内容:

[![Function app overview](img/f64ec5f385d3e4e5fdf24b24305c370d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D4Kc7tSd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/conteimg/2017/09/Screenshot-170918-112337.png)

## 存储 API 密钥

将 API 键直接存储在代码中并不是好的做法，所以我们将把它作为环境变量添加进来。这样 Azure 会以一种安全的方式存储它，我们可以在运行时获取密钥。

点击*应用设置*下的*配置功能*。在*应用设置*下，点击*添加新设置*，将其命名为`AirtableApiKey`，并将 API 密钥粘贴到值字段。

[![](img/036b6938dd22369d52b67ead07e34ada.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qZ87fAIf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/conteimg/2017/09/Screen-Shot-2017-09-19-at-10.49.30.png)

## 创建功能

点击右侧*功能*节点上的蓝色加号图标，并点击隐藏在*下方的*自定义功能**。点击 *HttpTrigger - C#* ，命名函数*重定向*，设置*授权级别*为*匿名*。点击*创建*。

[![](img/2645a5705d4489cd9e112c2e506a66c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zrXcISG_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/conteimg/2017/09/Screen-Shot-2017-09-19-at-10.46.55.png)

您将看到一个文本编辑器，其中包含一个 C#脚本 *run.csx* 的内容。去掉所有代码，我们要做自己的事。然而，在我们开始添加代码之前，我们需要指定路由模板。

## 配置路线

点击侧边栏中 *HttpRedirect* 功能下方的 *Integrate* 。将*允许的 HTTP 方法*设置为*选择的方法*，并在出现的*选择的 HTTP 方法*部分中，除了 *GET* 之外，清除所有的复选标记。这确保了只接受`HTTP GET`个请求。不是特别重要，但这是个好习惯。

接下来，将*路线模板*设置为`{path}`。这指定了我们想要捕获域之后的路径，并将其存储在一个名为`path`的变量中。`path`也是传递给我们的处理程序的参数的名称。

[![Configuring the route](img/7374491f7cd9c9cac424525f75b1dae1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U2-2KJhi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/conteimg/2017/09/Screenshot-170918-131339.png)

## 删除路径的 */api* 部分

默认情况下，你可以通过进入*<appname>. azure websites . net/API/*来访问你的函数，但是我们不想要 url 的 */api* 部分。

这可以通过编辑 app 的 *host.json* 文件来移除。我们编辑这个最简单的方法就是进入*<appname>. SCM . azure websites . net*，点击*调试控制台* > *CMD* 。然后导航到*站点* > *wwwroot* 并点击文件 *host.json* 旁边的铅笔图标。在这个文件中，添加以下 JSON:

```
{
    "http": {
        "routePrefix": ""
    }
} 
```

然后点击左上方的*保存*。

## 功能码

让我们回到 Azure 门户中的函数应用，点击侧边栏中的 *HttpRedirect* 。我们将开始添加处理请求的代码:

我们将引用*Json.NET*来简化处理来自 Airtable 的 JSON 响应。

```
#r "Newtonsoft.Json" 
```

然后我们添加必要的`using`语句。

```
using static System.Environment;  
using System.Net.Http;  
using System.Net.Http.Headers;  
using System.Net;  
using Newtonsoft.Json;  
using System.Threading.Tasks; 
```

我们还将添加一些模型类来表示来自 Airtable 的响应。`Record`类表示我们的 Airtable 表中的一行。它包含一些关于记录的元数据，而我们在表中输入的数据存储在`Fields`属性中。

```
public class Record  
{
    public string Id { get; set; }
    public Redirect Fields { get; set; }
    public DateTimeOffset CreatedTime { get; set; }
} 
```

`Redirect`类表示我们在表中输入的数据。

```
public class Redirect  
{
    public string Path { get; set; }
    public string Url { get; set; }
    public string Description { get; set; }
} 
```

接下来是`Run`函数。当有人导航到我们的功能应用程序时，将执行此操作。

我们感兴趣的部分是`path`参数。这将包含访问者导航到的路径。如果用户导航到*<appname>. azure websites . net/githubt*，`path`参数将包含值`github`。

```
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string path, TraceWriter log)  
{ 
```

我们首先创建一个`HttpClient`的实例，并指定基本 url。

```
var client = new HttpClient
    {
        BaseAddress = new Uri("https://api.airtable.com/")
    }; 
```

我们设置*授权*头来包含我们从 Airtable 获得的 API 密钥。我们从之前设置的环境变量中检索密钥。

```
client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetEnvironmentVariable("AirtableApiKey")); 
```

接下来，我们从 Airtable 表中检索正确的记录。我们使用从 API 文档中获得的 id 来访问数据库，然后指定我们想要访问数据库中的`Paths`表。最后，我们指定一个过滤器来只接收我们想要的记录。

你可以在 [Airtable 文档](https://support.airtable.com/hc/en-us/articles/203255215-Formula-Field-Reference)
中阅读关于过滤器的语法

```
var response = await client.GetStringAsync($"/v0/appxpeLm4zw7rVbMV/Paths?filterByFormula=AND(LOWER({{Path}}) = '{WebUtility.UrlEncode(path.ToLowerInvariant())}')"); 
```

得到响应后，我们创建一个匿名对象(还记得那些吗？)提供了解析 JSON 响应时 Json.NET 的定义。

```
var definition = new { Records = new List<Record>() };
    var result = JsonConvert.DeserializeAnonymousType(response, definition); 
```

然后，我们尝试从第一条记录中获取数据。如果没有，那么`redirect`将为空，我们返回一个`404`错误。

```
var redirect = result.Records.FirstOrDefault()?.Fields;

    if(redirect == null)
    {
        return new HttpResponseMessage(HttpStatusCode.NotFound); // Return 404
    } 
```

如果我们找到数据，我们将创建一个响应消息，指定浏览器应该重定向(`301`)。我们将*位置*头设置为浏览器应该重定向到的 URL，并返回响应消息。

```
var responseMessage = new HttpResponseMessage(HttpStatusCode.Redirect);
    responseMessage.Headers.Location = new Uri(redirect.Url);

    return responseMessage;
} 
```

见完整代码

```
#r "Newtonsoft.Json"

using static System.Environment;  
using System.Net.Http;  
using System.Net.Http.Headers;  
using System.Net;  
using Newtonsoft.Json;  
using System.Threading.Tasks;

public class Record  
{
    public string Id { get; set; }
    public Redirect Fields { get; set; }
    public DateTimeOffset CreatedTime { get; set; }
}

public class Redirect  
{
    public string Path { get; set; }
    public string Url { get; set; }
    public string Description { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string path, TraceWriter log)  
{
    var client = new HttpClient
    {
        BaseAddress = new Uri("https://api.airtable.com/")
    };
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetEnvironmentVariable("AirtableApiKey"));

    var response = await client.GetStringAsync($"/v0/appxpeLm4zw7rVbMV/Paths?filterByFormula=AND(LOWER({{Path}}) = '{WebUtility.UrlEncode(path.ToLowerInvariant())}')");

    var definition = new { Records = new List<Record>() };
    var result = JsonConvert.DeserializeAnonymousType(response, definition);

    var redirect = result.Records.FirstOrDefault()?.Fields;

    if(redirect == null)
    {
        return new HttpResponseMessage(HttpStatusCode.NotFound); // Return 404
    }

    var responseMessage = new HttpResponseMessage(HttpStatusCode.Redirect);
    responseMessage.Headers.Location = new Uri(redirect.Url);

    return responseMessage;
} 
```

# 就是这样！

通过向 Airtable 表*路径*添加路径，您将向您的“重定向器”添加新的重定向。你可以通过网络用户界面或他们为 iOS 和 Android 开发的优秀应用来实现这一点。

编码快乐！