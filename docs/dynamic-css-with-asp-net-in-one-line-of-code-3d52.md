# 带有 ASP.NET 的动态 CSS 在一行代码中

> 原文：<https://dev.to/adamkdean/dynamic-css-with-asp-net-in-one-line-of-code-3d52>

无论您希望每个浏览器都有一个单独的表单，还是希望跟踪人们何时从您的站点打印页面(就像我的情况)，如何让代码在 CSS 文件中运行呢？你不能。你如何得到一个 ASPX 文件来提供 CSS？你不能。但是和 House MD 一样，我有答案，最终会告诉你。

您要做的是创建一个 ASPX 文件，在 Page_Load 事件中，编写您想要的任何跟踪或逻辑代码，然后简单地重定向页面，而不输出任何内容。

```
protected void Page_Load(object sender, EventArgs e)
{
    // code here
    Response.Redirect("style.css", true);
} 
```

现在你有了它，带有 ASP.NET 的动态 CSS 在一行代码中。