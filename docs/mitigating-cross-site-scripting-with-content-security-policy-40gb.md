# 使用内容安全策略减少跨站点脚本

> 原文：<https://dev.to/dustinsoftware/mitigating-cross-site-scripting-with-content-security-policy-40gb>

在本帖中，我们将探讨如何使用内容安全策略(CSP)作为一种深度防御技术来阻止脚本注入攻击。

当构建托管用户生成内容的网站时，例如:

```
Great to be here!
<script>window.location='https://example.com'</script> 
```

Enter fullscreen mode Exit fullscreen mode

有必要对用户生成的内容进行编码，这样浏览器就不会误认为是标记，执行不可信的脚本。对于纯文本这很容易做到，但是如果一个页面需要呈现用户生成的 HTML 呢？下面是一个包含内嵌 Javascript 的 HTML 示例，浏览器可以执行:

```
<p>Great to <b>be</b> here!</p>
<img src="" onerror="alert(0)" />
<a href="javascript:alert(0)">Hi</a>
<script>window.location='https://example.com'</script> 
```

Enter fullscreen mode Exit fullscreen mode

此内容必须在呈现前进行清理。诸如 HTMLAgilityPack 或 DOMPurify 之类的库提供了一种方法来解析 HTML，并去除已知的执行脚本的元素或属性。

净化很重要，但是如果攻击者发现了绕过过滤器的方法呢？这就是[内容安全政策](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)发挥作用的地方。

如果在检索页面时出现了`Content-Security-Policy`头，并且包含一个`script-src`定义，那么脚本将被阻止，除非它们与策略中指定的源之一相匹配。一个策略可能看起来像这样:

```
script-src 'self'; object-src 'none'; base-uri 'none'; 
```

Enter fullscreen mode Exit fullscreen mode

该政策不允许:

*   外部脚本与当前页面不在同一个域中。
*   内联脚本元素，如`<script>`
*   评估过的 Javascript，比如`<img src="" onerror="alert(0)" />`
*   基本元素，这可能会破坏从相对路径加载的脚本
*   可以承载交互式内容的对象元素，如 Flash

### 将内联脚本列入白名单

有时有必要在页面上运行内联脚本。在这些情况下，`script`元素上的`nonce`属性可用于将您控制的脚本列入白名单。

```
<script nonce="00deadbeef">doSomething()</script> 
```

Enter fullscreen mode Exit fullscreen mode

CSP 中必须存在匹配的 nonce，脚本才能运行。为了与旧浏览器兼容，`unsafe-inline`允许在不支持`nonce`标签的情况下运行脚本。

```
script-src 'self' 'nonce-00deadbeef' 'unsafe-inline'; object-src 'none'; base-uri 'none'; 
```

Enter fullscreen mode Exit fullscreen mode

重要的是，这个随机数是从加密随机数生成器中导出的，这样攻击者就无法猜测未来的随机数。英寸 NET，`RNGCryptoServiceProvider.GetBytes`可以用来填充一个 16 字节的数组:

```
using (var random = new RNGCryptoServiceProvider())
{
    byte[] nonce = new byte[16];
    random.GetBytes(nonce);
    return Convert.ToBase64String(nonce);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 将外部脚本列入白名单

`strict-dynamic`可用于允许第三方域上托管的脚本由您控制的脚本加载。然而，在撰写本文时，并非所有主流浏览器都支持这一功能，因此应该使用主机白名单作为后备，直到它得到广泛支持。

```
script-src 'self' 'nonce-00deadbeef' 'unsafe-inline' 'strict-dynamic' https://example.com; object-src 'none'; base-uri 'none'; 
```

Enter fullscreen mode Exit fullscreen mode

当使用`strict-dynamic`时，您还需要向引用的任何外部脚本添加一个`nonce`。

```
<script nonce="00deadbeef" src="https://example.com/analytics.js" /> 
```

Enter fullscreen mode Exit fullscreen mode

> **注:**
> 
> 小心你列入白名单的来源。如果任何端点返回 JSONP 并且无法清理回调，就有可能注入代码。例如:
> 
> ```
> <script src="https://example.com/getuser?callback=window.location='http://google.com';test"></script> 
> ```
> 
> 可能在 JSONP 响应中返回`window.location='http://google.com';test({})`，这将导致任意代码被执行！

您还可以定义其他策略来加强站点的安全性，比如限制样式表的加载位置。这篇文章只关注减轻跨站点脚本攻击。

### 进一步阅读

*   [CSP 3](https://www.w3.org/TR/CSP3/)
*   [CSP 评估器](https://csp-evaluator.withgoogle.com/)可用于发现策略中的漏洞。
*   [CSP 简介](https://csp.withgoogle.com/docs/index.html)
*   [Mozilla 文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

感谢 Bradley Grainger 和 Kyle Sletten 对这个实现的评论。