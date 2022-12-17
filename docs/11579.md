# 用 WebAPI 强制 JSON

> 原文：<https://dev.to/adamkdean/force-json-with-webapi-56km>

有时当你通过浏览器调用一个 WebAPI 时，你会得到 XML。这可能使它更容易阅读，但当我们真正想要的是 JSON 时，它真的没有帮助我们。将以下代码片段放入 Global.asax.cs 中 Application_Start()的末尾，将会强制您的 WebAPI 应用程序始终发送 JSON。

```
GlobalConfiguration.Configuration.Formatters.Clear();
GlobalConfiguration.Configuration.Formatters.Add(new JsonMediaTypeFormatter()); 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。