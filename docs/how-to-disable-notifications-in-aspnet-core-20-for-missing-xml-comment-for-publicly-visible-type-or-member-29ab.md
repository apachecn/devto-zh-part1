# 如何在 ASP.NET 核心 2.0 中禁用“公共可见类型或成员缺少 XML 注释”的通知

> 原文：<https://dev.to/coolgoose/how-to-disable-notifications-in-aspnet-core-20-for-missing-xml-comment-for-publicly-visible-type-or-member-29ab>

我已经开始和 ASP.NET 一起玩了，因为我很久以前就想开始学习 C#，但一直没有时间。现在，随着 Core 2.0 的推出，它的影响力越来越大。

不幸的是，现在最大的问题是文档很少。浏览 swagger 教程需要启用文档的 xml 生成，以便正确更新 swagger 定义。

一旦完成，你会遇到一个很好的警告 *[公共可见类型或成员](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-messages/cs1591)* 的 XML 注释丢失，不幸的是，这是在 Visual Studio 代码中开发时使用的 pita，因为它在启动调试器时不断地烦扰你。记下报告的错误代码 *CS1591* 。

现在我们有了。csproj 文件，修复相当简单。
你只需要将`<noWarn>1591</noWarn>`(不含 **CS** )包含在*文档文件*定义下即可。

```
<PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Debug|AnyCPU'">
    <DocumentationFile>bin\Debug\netcoreapp2.0\Craidd.xml</DocumentationFile>
    <noWarn>1591</noWarn>
</PropertyGroup> 
```

Enter fullscreen mode Exit fullscreen mode