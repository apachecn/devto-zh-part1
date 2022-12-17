# 轻松绕过同源策略

> 原文：<https://dev.to/adamkdean/easily-bypass-same-origin-policy-4i2m>

如果你曾经试图使用 AJAX 或 iFrame 加载另一个 javascript 网站，但没有成功，那是因为同源策略。引用维基百科:

> 在计算中，同源策略是许多浏览器端编程语言(如 JavaScript)的重要安全概念。该策略允许来自同一站点的页面上运行的脚本(方案、主机名和端口号的组合)在没有特定限制的情况下访问彼此的方法和属性，但是阻止对不同站点上的页面上的大多数方法和属性的访问。

这意味着你只能抓取当前站点上的文件/脚本，这阻止了你抓取外部资源。当你试图做一些合法的事情时，这可能是一种痛苦。

如果你有后端访问，你可以总是有一个 PHP 页面获取 URL 并打印出内容，或者你可以发出`Access-Control-Allow-Origin:`头，允许某些网站甚至通配符完全打开它。但是当你没有这个权限的时候，当你只有 HTML 和 JS 可以支配的时候，你怎么办？

您可以使用 jQuery 的`getJSON`函数从外部源获取 JSON，外部提供者如 [AnyOrigin](http://anyorigin.com/) 或 [whateverorigin](http://whateverorigin.org/) 将为您获取内容并在 JSON 对象中返回它们。太神奇了。

```
// use jQuery to grab the contents of an url
var url = encodeURIComponent("http://www.google.com");
$.getJSON('http://whateverorigin.org/get?url=' + url + '&callback=?', 
    function(data) {
        var html = data.contents;
        // your code here
    }
); 
```

Enter fullscreen mode Exit fullscreen mode

工作很好，虽然你必须依赖第三方。

如果你能接受，那你也应该能接受！