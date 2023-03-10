# 保护您的 ASP.NET 应用程序

> 原文：<https://dev.to/overrideveloper/securing-your-aspnet-application>

### 安全是软件开发中一个非常重要的话题。在开发软件时，让你的应用程序变得不安全并容易受到攻击比采取安全措施更容易。有时候，当涉及到安全问题时，我会犯懈怠的错误。ðŸ˜…ðŸ˜…

在本文中，我将使用 ASP.NET MVC 模板作为讨论的焦点。从安全角度来看，MVC 模板非常脆弱。可以说模板更倾向于简单而不是安全。

### 这里有一些关于如何保护你的 ASP.NET MVC 应用程序的提示

#### 1。哈希密码

我知道这是软件开发中一种显而易见的做法。但是，我还是觉得有必要提一下。散列密码是身份验证的一个非常重要的部分，但是很容易忘记，特别是如果你正在编写自己的自定义身份验证代码。

此外，哈希算法的选择也很重要。千万不要用 MD5 来哈希密码，MD5 超级容易破解。ðŸ˜'ðŸ˜'

#### 2。使用强密码验证逻辑

尝试让你的密码验证尽可能的“强”。下面是我个人最喜欢的密码验证。

```
UserManager.PasswordValidator = new PasswordValidator
{
    RequiredLength = 6,
    RequireNonLetterOrDigit = true,
    RequireDigit = true,
    RequireLowercase = true,
    RequireUppercase = true,
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 3。简短的身份验证错误消息

使您的身份验证错误消息尽可能简短。例如，在登录失败时，您的错误消息应该是“用户名/密码无效”,而不是指明哪一个无效，因为这会给潜在的黑客提供足够的信息。

此外，在密码重置中，如果输入的电子邮件在系统中不存在，而不是返回错误消息，显示成功页面。

#### 4。打开自定义错误

默认情况下，每当出现错误时，模板都会显示异常的完整堆栈跟踪。这是非常敏感的信息，应该隐藏起来。这可以通过在 web.config 文件中添加自定义错误标记并将其模式设置为 on 来完成。

```
<system.web>
    <customErrors mode="On"></customErrors>
</system.web> 
```

Enter fullscreen mode Exit fullscreen mode

#### 5。保护好你的饼干！

默认情况下，模板的 cookies 可以被其他站点的 JavaScript 访问。这些 cookies 也可以不加密地通过网络发送。这可以通过向 web.config 文件添加 httpcookies 标记来解决。

```
<system.web>
    <httpCookies httpOnlyCookies="true" requireSSL="false"/>
</system.web> 
```

Enter fullscreen mode Exit fullscreen mode

如果您在您的站点上使用 SSL，那么 requireSSL 应该设置为 true。

#### 6。去掉不必要的标题！

默认情况下，模板会在每个请求中发送几个 HTTP 头。这些头包含敏感信息，如服务器类型、ASP.NET 版本和 MVC 版本。我们可以通过将 enableVersionHeader 设置设置为 false 来解决此问题。

```
<system.web>
    <httpRuntime targetFramework="4.5.2" enableVersionHeader="false"/>
</system.web> 
```

Enter fullscreen mode Exit fullscreen mode

这样做之后，我们清除自定义标题

```
<system.webServer>
    <httpProtocol>
        <customHeaders>
            <clear />
        </customHeaders>
    </httpProtocol>
</system.webServer> 
```

Enter fullscreen mode Exit fullscreen mode

#### 7。重命名您的 cookies。

在 MVC 模板中，有三种基本的 cookie:应用程序 cookie、防伪令牌和会话 cookie。我们应该重命名这些 cookies，使用户不知道我们正在使用的服务器的类型。

###### 重命名会话 cookie

```
<system.web>
    <sessionState cookieName="custom_name" />
</system.web> 
```

Enter fullscreen mode Exit fullscreen mode

###### 重命名防伪令牌

这是在 Global.asax.cs 文件
中完成的

```
protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
            //added line
            ConfigureAntiForgeryTokens();
            //end of added line
        }
//added method
private static void ConfigureAntiForgeryTokens()
{
    AntiForgeryConfig.CookieName = "custom_name";

    // AntiForgeryConfig.RequireSsl = true;
}
//end of added method 
```

Enter fullscreen mode Exit fullscreen mode

如果您的站点将使用 Ssl，请取消最后一行的注释。

###### 重命名应用程序 cookie

这是在启动时完成的。App_Start 文件夹的 Auth.cs 文件

```
public void ConfigureAuth(IAppBuilder app)
{
    app.UseCookieAuthentication(new CookieAuthenticationOptions
    {
        AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
        LoginPath = new PathString("/Account/Login"),
        CookieName = "custom_name"
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 8。禁用跟踪

```
<system.web>
  <trace enabled="true" localOnly="true"/>
</system.web> 
```

Enter fullscreen mode Exit fullscreen mode

#### 9。将 403 -禁止响应显示为 404

当您在 IIS 和 ASP.NET MVC 中导航到一个目录时，它会导致返回一个 403 - Forbidden 响应。这基本上意味着目录浏览被禁用。目录浏览存在安全风险，因为它允许使用所有连接字符串访问 web.config 文件。当目录浏览返回一个 403 时，它会带来一个问题，因为它会告诉潜在的攻击者你正在使用 IIS，而实际上那里有一个文件夹。
我们通过将 403 响应替换为标准的 404 来解决这个问题。

首先，我们将 httpErrors 添加到 web.config 文件中。

```
<system.webServer>
  <!-- Custom error pages -->
  <httpErrors errorMode="Custom" existingResponse="Replace">
    <!-- Redirect IIS 403 Forbidden responses to the not found action method on error controller -->
    <error statusCode="403" subStatusCode="14" responseMode="ExecuteURL" path="/error/notfound" />
  </httpErrors>
</system.webServer> 
```

Enter fullscreen mode Exit fullscreen mode

然后我们关闭 IIS 的默认文档处理。这将阻止 IIS 在导航到文件夹时返回默认文档(使用所谓的礼貌重定向)，例如，导航到包含 index.html 文件的“/Folder”将不会返回“/Folder/index.html”。如果它重定向到页面 url 中的默认文档，就会向潜在攻击者显示目录的路径，这是我们不希望看到的。

```
<system.webServer>
  <defaultDocument enabled="false"/>
</system.webServer> 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的几个技巧，你可以使你的 ASP.NET 程序更加安全。ðÿ˜感谢你的阅读！ðŸ˜„ðŸ™‚