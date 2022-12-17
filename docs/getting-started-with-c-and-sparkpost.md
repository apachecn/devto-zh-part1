# C#和 SparkPost 入门

> 原文：<https://dev.to/sparkpost/getting-started-with-c-and-sparkpost>

### 在 C#中有更好的发送电子邮件的方法

发送电子邮件是 C#应用程序非常常见的需求。想想几乎每个应用程序都需要处理的一些用例:新用户欢迎、事件通知、密码重置等等。这些交易电子邮件是非常繁重的工作，它们到达你的用户手中是非常重要的。作为一名 C#开发人员，完成它们的最好方法是什么？

有几个 C#电子邮件库，包括 system.net.mail、systems.web.mail. SmtpClient、MailKit、MimeKit 等。，但它们都有一个共同点:你需要一个 SMTP 服务器来使用它们。即使你有访问服务器的权限，它也可能无法确保你的信息快速一致地到达用户的收件箱。使用 SparkPost 电子邮件传递服务是一种更简单的方式。

让我们试一试。以下是如何使用 SparkPost 和 C#发送消息。

### 您好，世界！我用 C#和 SparkPost 发送电子邮件

使用 C#和 SparkPost 发送电子邮件的第一步是[注册一个 SparkPost 开发者账户](https://pages.sparkpost.com/bl-account-signup-generic.html?src=Blog&pc=bl-rfi-generic-2017&utm_source=blog&utm_medium=blog&utm_campaign=all&utm_content=signup)并配置好你的发送域。按照我们的[入门指南](https://www.sparkpost.com/docs/getting-started/getting-started-sparkpost/)让您的帐户设置正确的发送域。我就在这里等。

设置好帐户，配置好发送域，准备好 API 密匙，就可以使用 C#发送第一封电子邮件了。从你的 Nuget [包管理器控制台](http://docs.nuget.org/consume/package-manager-console) :
创建一个应用程序并安装 Sparkpost Nuget 包

```
PM> Install-Package SparkPost 
```

Enter fullscreen mode Exit fullscreen mode

安装 SparkPost 后，我们可以格式化并发送我们的第一封 C#电子邮件:

```
// start by creating a new SparkPost transmission
var transmission = new SparkPost.Transmission();
transmission.Content.From.Email = "me@YOURSENDINGDOMAIN.com";
transmission.Content.Subject = "Hello from SparkPost!";
transmission.Content.Text = "This is a test email.";

// add recipients who will receive your email
var recipient = new Recipient
{
    Address = new Address { Email = "you@YOUREMAILADDRESS.com" }
};
transmission.Recipients.Add(recipient);

// create a new API client using your API key
var client = new Client("<YOUR API KEY HERE>");

// if you do not understand async/await, use the sync sending mode:
client.CustomSettings.SendingMode = SendingModes.Sync;
var response = client.Transmissions.Send(transmission);

// if you understand async/await, use the default async sending mode:
// var response = client.Transmissions.Send(transmission);

// to get the transmission id back from SparkPost
// response.Result.Id;

// to get the status code of the SparkPost response
// response.Result.StatusCode;

// to get the raw json result from SparkPost
// response.Result.Content; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！启动应用程序，您应该会在收件箱中看到一条测试消息。最棒的是。您不必管理任何服务器，监视任何服务，也不必担心消息是否会到达它的接收者。你负责决定发送什么和给谁，SparkPost 负责剩下的事情。

### 敬请关注更多 C#电子邮件技巧

多亏了一个简单的 API，你正在成为一名 C#电子邮件高手。你有一个工作的发送域，你已经发送了你的第一封邮件，不久你将征服电子邮件世界！

也就是说，你的旅程还没有结束。你真的想通过用代码构建电子邮件模板来将内容和设计结合起来吗？当然不是！请继续关注，我将向您展示如何将消息内容从 API 调用中分离出来，并预先存储消息模板，从而进一步简化您的代码。

在那之前，[注册 SparkPost](https://pages.sparkpost.com/bl-account-signup-generic.html?src=Blog&pc=bl-rfi-generic-2017&utm_source=blog&utm_medium=blog&utm_campaign=all&utm_content=signup) ，[建立你的域名](https://www.sparkpost.com/docs/getting-started/getting-started-sparkpost/#preparing-your-from-address)，查看我们[伟大的 API 文档](https://developer.sparkpost.com/api/)！如果你有任何关于 C#和 SparkPost 的问题，欢迎来到我们的社区 slack 的#csharp 频道。

*本帖最初发表于[sparkpost.com](https://www.sparkpost.com/blog/csharp-and-sparkpost/)T3】*