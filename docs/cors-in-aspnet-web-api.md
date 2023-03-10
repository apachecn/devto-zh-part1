# ASP.Net 的 CORS Web API

> 原文：<https://dev.to/overrideveloper/cors-in-aspnet-web-api>

### 什么是跨原点 http 请求？

这是一个资源向不同域、协议、端口中的资源发出的请求。例如，位于“[http://domain1.com](http://domain1.com)的 HTML 页面向位于“[http://domain2.com](http://domain2.com)的资源发出请求。

### 我们为什么要说这个？

嗯，这是一件“大事”,因为出于安全考虑，浏览器限制从脚本内部发起跨源请求。所以网络应用程序只能在其域内发出请求。有点限制了应用程序，你不觉得吗？这就是 CORS 的用武之地。

### 什么是 CORS？

CORS 主张跨产地资源共享。这是一个标准，它通过添加 HTTP 头来工作，允许服务器描述允许使用 web 浏览器读取信息的源集合以及允许的请求类型。

此外，对于可能对服务器数据产生副作用的 HTTP 请求方法(通常是 HTTP 请求而不是 GET)，规范要求浏览器“预先检查”请求，即询问服务器支持什么方法。它使用 HTTP OPTIONS 请求方法来实现这一点，然后，在得到服务器的“批准”后，它发送实际的 HTTP 请求。

### ASP.NET CORS WEB API 2

在本文中，我将展示如何在 ASP.NET WEB API 服务器上启用 CORS。对于本文，我假设您已经创建了一个 Web API 项目。

可以通过几种方式启用 CORS，如下所示:

#### 启用每个控制器的 CORS。

首先，安装 CORS 软件包。在软件包管理器控制台中，键入以下命令:`Install-Package Microsoft.AspNet.WebApi.Cors`。

导航到 App_Start 文件夹并打开 WebApiConfig.cs 文件。修改 WebApiConfig。注册以反映以下更改:

```
using System.Web.Http;
namespace CORSWithWebAPI
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // Modification
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
} 
```

然后，将[EnableCors]属性添加到控制器类，指定源、头和方法。如果在控制器类上设置[EnableCors],它将应用于控制器上的所有操作。要禁用某个操作的 CORS，请向该操作添加[DisableCors]属性。

```
using System.Net.Http;
using System.Web.Http;
using System.Web.Http.Cors;

namespace CORSWithWebAPI.Controllers
{
    [EnableCors(origins: "http://client.domain", headers: "*", methods: "*")]
    public class SampleController : ApiController
    {
         public HttpResponseMessage Post() { ... }

        [DisableCors]
        public HttpResponseMessage PutItem(int id) { ... }   
    }
} 
```

将方法和头设置为“*”意味着允许所有头和所有方法。也可以设置具体的方法和头。

#### 启用 CORS 每次动作

这类似于启用每个控制器。除了[EnableCors]属性被添加到动作而不是控制器。

```
public class SampleController : ApiController
{
    [EnableCors(origins: "http://client.domain", headers: "*", methods: "*")]
    public HttpResponseMessage GetSample(int id) { ... }
} 
```

#### 全球启用 CORS

若要为所有 Web API 控制器启用 CORS，请将一个 EnableCorsAttribute 实例传递给 WebApiConfig.cs 文件中的 enable CORS 方法。

```
public static class WebApiConfig
{
    public static void Register(HttpConfiguration config)
    {
        var cors = new EnableCorsAttribute("client.domain", "*", "*");
        config.EnableCors(cors);
    }
} 
```

### CORS 也可以从 Web 上启用。项目的配置文件

在 web.config 文件中。在`<system.webserver></system.webserver>`部分下。添加以下更改:

```
<system.webServer>
    <handlers>
      <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
      <remove name="OPTIONSVerbHandler" />
      <remove name="TRACEVerbHandler" />
      <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
    </handlers>

    <!-- New addition -->
    <httpProtocol>
    <customHeaders>
        <add name="Access-Control-Allow-Origin" value="*" />
        <add name="Access-Control-Allow-Credentials" value="true"/>
        <add name="Access-Control-Allow-Headers" value="Content-Type" />
        <add name="Access-Control-Allow-Methods" value="GET, POST, PUT, DELETE, OPTIONS" />
    </customHeaders>
    </httpProtocol>
    <!-- End of new addition -->
  </system.webServer> 
```

这将为项目中的所有控制器全局设定 CORS。

### 飞行前请求错误

我之前解释过什么是飞行前请求。在 Web API 中，当从脚本发出预检请求时，即使一切都设置正确，预检请求通常也会返回 Http 错误代码 405。这可以通过对 web.config 和 Global.asax 文件稍作修改来解决。

在 web.config 文件中:

```
<system.webServer>
    <handlers>
      <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
      <remove name="OPTIONSVerbHandler" />
      <remove name="TRACEVerbHandler" />
      <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
      <!-- New addition -->      
      <add name="OPTIONSVerbHandler" path="*" verb="OPTIONS" modules="ProtocolSupportModule" requireAccess="None" responseBufferLimit="4194304" />  
      <!-- End of new addition -->  
    </handlers>
    <httpProtocol>
    <customHeaders>
        <add name="Access-Control-Allow-Origin" value="*" />
        <add name="Access-Control-Allow-Credentials" value="true"/>
        <add name="Access-Control-Allow-Headers" value="Content-Type" />
        <add name="Access-Control-Allow-Methods" value="GET, POST, PUT, DELETE, OPTIONS" />
    </customHeaders>
    </httpProtocol>
  </system.webServer> 
```

在 Global.asax 文件中:

```
public class WebApiApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }

        //Added code
        protected void Application_BeginRequest(object sender, EventArgs e)
        {
            if (HttpContext.Current.Request.HttpMethod == "OPTIONS")
            {
                HttpContext.Current.Response.Flush();
            }
        }
        //end of added code
    } 
```

我希望这有助于您在 Web API 项目中轻松启用 CORS。干杯！