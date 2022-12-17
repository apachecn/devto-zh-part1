# 对服务人员有效使用 CSP 随机数

> 原文：<https://dev.to/paul_kinlan/using-csp-nonces-effectively-with-service-worker-1c12>

在最近的一个项目中，我想在服务器、服务人员和客户端之间尽可能多地共享逻辑。该项目本质上是一个简单的 RSS 提要阅读器，它获取 RSS 提要，解析数据并将它们合并到一组漂亮的列中(很像 TweetDeck)，还有一个合并的列表。

因为我获取 RSS 提要并显示在我的页面中，所以我需要尽可能确定它没有做任何邪恶的事情。我可以尽可能地净化输入，但是我知道我自己的能力，我确信有人可以操纵 RSS 提要，以至于我最终会运行脚本，导入图像或任何其他第三方在我的站点的上下文中。

web 平台提供了通过内容安全策略(CSP)锁定网站的能力。CSP 可以锁定外部资源，我们可以从中请求上下文，如脚本、样式、图像等。你甚至可以锁定页面在线运行脚本的能力——这可以防止所有 XSS 庄园类型的攻击。

将它添加到应用程序中非常简单。

```
`default-src 'self';` 
```

Enter fullscreen mode Exit fullscreen mode

然而…我有很多问题。

1.  我在页面上内联生成样式，因此我需要内联运行脚本。
2.  我需要包括谷歌分析，这需要一个内嵌脚本在页面上运行。

CSP 允许您打开一个名为`unsafe-eval`的脚本选项来运行脚本和样式，但是这几乎绕过了 CSP 提供的任何保护。

为了运行内联脚本并仍然拥有 CSP 的保护，CSP 提供了一些工具。我用的那个叫做‘nonce’。nonce 是您在 CSP HTTP 头上设置的随机 id，它与相关的内联脚本一致。

**HTTP 报头上的 CSP 字符串**

```
`default-src 'self'; script-src 'self' https://www.googletagmanager.com https://www.google-analytics.com 'nonce-script-${nonce.analytics}'; connect-src 'self'; img-src 'self' data: https://www.google-analytics.com; style-src 'self' 'nonce-style-${nonce.style}' 
```

Enter fullscreen mode Exit fullscreen mode

**使用 nonce 的内联脚本**

```
<script src="https://www.googletagmanager.com/gtag/js?id=1111"></script>
<script nonce="script-{nonce.analytics}">
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', '{{=it.config.then(config=>config.site.googleAnalytics)}}');
</script> 
```

Enter fullscreen mode Exit fullscreen mode

当我们用 CSP 保护网站时，上面的代码工作得很好，使分析正确工作变得简单。

对于每一个 web 请求，你需要有一个唯一的‘nonce’值，我通过`{nonce.analytics}`来实现，它是我在服务器上生成的一个值，通过一个模板来应用。如果您重复使用 nonce 值，浏览器将拒绝执行脚本中的内容。

我在生成随机数时遇到了一点麻烦。我需要的东西，将创造一个独特的价值，不会被同一用户重复使用。我觉得“[source]-[date . now+request-count]”格式的 nonce 值就足够了。

“source”允许我向 nonce 添加一个名称空间，而 date.now() +不断增加的请求计数给了我一组相对稳定的不可重复的值。

我使用下面的函数生成随机数:

```
function generateIncrementalNonce(source) {
  let val = 0;
  let max = Math.pow(10, 3); // Date + pow 3 gets us close to max number;

  const generate = () => {
    let now = max * +new Date();
    if(val >= max) val = 0;
    else val++;
    return (source !== undefined ? source : '') + (now + val).toString();
  }

  return generate;
}; 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错。但是，我将我的所有页面缓存在一个服务工作器中，这意味着如果我只是从缓存中提供内容，nonce 值将被重用，因此不会被执行。

幸运是，我在我的服务器和服务人员之间共享逻辑，这允许我在代码的一个中心位置生成我需要的任何东西。我在我的`generateIncrementalNonce`函数中使用“source”参数将“server”或“service-worker”添加到 nonce 值的前面，我在服务器和服务 worker 的每个请求处理程序中都这样做了。使用这个 source 参数意味着我可以保证通过服务器生成的 nonce 值永远不会与通过服务工作器加载的页面冲突。

这种模式很适合我。它允许我允许谷歌分析所需的内联脚本，同时阻止任何第三方在我的页面中注入或运行不受信任的代码。

下面是我在项目中使用的代码。我的页面中有许多不同的地方需要 nonce 值，我为每个请求生成它们，然后同时将它们应用于我的模板函数和 HTTP 头。

#### common.js -共享逻辑

```
function generateCSPPolicy(nonce) {
  return `default-src 'self'; script-src 'self' https://www.googletagmanager.com https://www.google-analytics.com 'nonce-script-${nonce.analytics}'; connect-src 'self'; img-src 'self' data: https://www.google-analytics.com; style-src 'self' 'nonce-style-${nonce.style}' 'nonce-style-${nonce.inlinedcss}';`;
};

function generateIncrementalNonce(source) {
  let val = 0;
  let max = Math.pow(10, 3); // Date + pow 3 gets us close to max number;

  const generate = () => {
    let now = max * +new Date();
    if(val >= max) val = 0;
    else val++;
    return (source !== undefined ? source : '') + (now + val).toString();
  }

  return generate;
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### service-worker.js - fetch 处理器

```
const generator = generateIncrementalNonce('service-worker');
let nonce = {
  analytics: generator(),
  inlinedcss: generator(),
  style: generator()
};

// Call the route handler with all data needed
let response = all(nonce, {
  dataPath: paths.dataPath,
  assetPath: paths.assetPath
}).then(r => setHeader(r, 'Content-Security-Policy', generateCSPPolicy(nonce)));;
e.respondWith(response); 
```

Enter fullscreen mode Exit fullscreen mode

#### server.js -请求处理程序

```
const generator = generateIncrementalNonce('server');

let nonce = {
  analytics: generator(),
  inlinedcss: generator(),
  style: generator()
};

res.setHeader('Content-Security-Policy', generateCSPPolicy(nonce));

// Call the route handler with all data needed
all(nonce, {
      dataPath: `${paths.dataPath}${hostname}.`,
      assetPath: paths.assetPath 
    })
    .then(response => {
      node.responseToExpressStream(res, response.body)
    }); 
```

Enter fullscreen mode Exit fullscreen mode