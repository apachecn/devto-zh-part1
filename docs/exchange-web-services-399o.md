# Exchange Web 服务

> 原文：<https://dev.to/funkysi1701/exchange-web-services-399o>

在我工作的地方，我们使用一种非常老式的方法来记录员工在这个国家的位置:Excel！

多年来，我一直试图说服员工使用日历作为交换。Outlook 非常适合同时查看一两个人的日历，但是很快就会变得难以管理，无法查看十个或更多人的空闲时间。

最近，我开始研究查询这些信息以给出自定义视图有多容易。

微软提供了一个 API 来查询交换信息，称为交换 Web 服务或 EWS。我只在我的 Exchange 2010 设置中使用过 EWS，但文档中提到了使用 Exchange 2007 和更早版本或 Exchange online。

以下是我尝试过的基本内容。启动 Visual Studio 并从 nuget 安装 EWS。

```
Install-Package Microsoft.Exchange.WebServices 
```

Enter fullscreen mode Exit fullscreen mode

我从一个简单的控制台应用程序开始，看看它是如何工作的，然后将其扩展到一个 MVC 网站。我发现直接查询 exchange 很慢，但是在数据库中缓存信息很容易。

首先，您需要通过指定您正在使用的 Exchange 版本来创建一个 Exchange 服务对象。

```
ExchangeService service = new ExchangeService(ExchangeVersion.Exchange2010); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要传递用于访问 Exchange 的 URL。

```
service.Url = new Uri("mail.example.com"); 
```

Enter fullscreen mode Exit fullscreen mode

要从 exchange 访问信息，您需要传递一些 Exchange 凭据，最好是能够查看您想要查看的所有日历的用户名和密码。

```
service.Credentials = new WebCredentials("username","password"); 
```

Enter fullscreen mode Exit fullscreen mode

接下来传递拥有您想要查看的日历的用户的电子邮件地址。

```
service.ImpersonatedUserId = new ImpersonatedUserId(ConnectingIdType.SmtpAddress, "me@example.com"); 
```

Enter fullscreen mode Exit fullscreen mode

我的特定 exchange 服务器具有自签名 SSL 证书，远程客户端不会信任该证书。下面一行忽略这个验证检查，并使我的程序连接。

```
System.Net.ServicePointManager.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) => true; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经连接到 exchange，我们只需要几行代码来查找日历中的事件。

```
// Initialize the calendar folder object with only the folder ID.
CalendarFolder calendar = CalendarFolder.Bind(service, WellKnownFolderName.Calendar, new PropertySet());

// Set the start and end time and number of appointments to retrieve.
CalendarView cView = new CalendarView(startDate, endDate);

// Limit the properties returned to the appointment's subject, start time, and end time.
cView.PropertySet = new PropertySet(AppointmentSchema.Subject, AppointmentSchema.Start, AppointmentSchema.End);

// Retrieve a collection of appointments by using the calendar view.
FindItemsResults<Appointment> appointments = calendar.FindAppointments(cView); 
```

Enter fullscreen mode Exit fullscreen mode

现在有了 appointments 对象，可以用 foreach 循环遍历每个元素。在我的例子中，我将每个元素分配给一个变量，然后我可以做我喜欢的事情(显示在控制台窗口中，保存到数据库中，显示在 MVC 网站中。)

```
foreach (Appointment a in appointments)
{
    if (a.Subject != null)
    {
        subject += a.Subject.ToString();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我的网站查询一个 SQL 数据库，我可以很容易地用一个控制台应用程序填充它，该应用程序在一天中定期运行。

我还想对这个项目做更多的事情，因为这只是您可以使用 Exchange Web 服务做的基本工作。因此，随着我扩展它的功能并学习新的做事方法，期待更多关于这个主题的博客帖子。