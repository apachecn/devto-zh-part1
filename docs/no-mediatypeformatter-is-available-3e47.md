# 没有可用的 MediaTypeFormatter

> 原文：<https://dev.to/adamkdean/no-mediatypeformatter-is-available-3e47>

您可能在 ASP.NET Web API 中遇到过错误消息`No MediaTypeFormatter is available to read an object of type T from content with media type 'text/plain'.`。今晚轮到我面对这个烦人的小“问题”了。

我试图将`{Username:"testuser", Password:"testpass"}`发布到一个 API 控制器，该控制器收到一个类似如下的视图模型:

```
public class UserViewModel
{
    public string Username { get; set; }
    public string Password { get; set; }
}

public HttpResponseMessage Post(UserViewModel value)
{
    // do something
} 
```

唯一的问题是它没有找到控制器动作。抛出错误信息，通常会适得其反。

在浏览了大量 StackOverflow 的帖子并拒绝它们后，发现第一个帖子实际上是相关的。我使用 Chrome 扩展(Postman)来帮助 REST 调用，即使我选择了 JSON，它仍然以`plain/text`而不是`application/json`的身份发送请求。

我将`Content-type`标题设置为`application/json`，它开始工作。