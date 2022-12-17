# HTTP 客户端超时

> 原文：<https://dev.to/brightdevs/http-client-timeouts-1c2f>

我们已经提到了[暂停](https://dev.to%20post_url%202017-10-23-the-importance-of-timeouts%20)的重要性，并描述了最重要的[相关的 JDBC 旋钮](https://dev.to%20post_url%202017-10-31-database-timeouts%20)。我想关注的超时的下一个方面是使用 API 客户端。特别是目前最流行的 HTTP 客户端。我们将回顾几个流行的 HTTP 客户端库和它们关于超时的配置。

[![Waiting](img/5b8961e2feb6b8d08e9db47e324d08c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QgMMjlpg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iuw5bjtpa3zh1fvoo9q1.jpg)

## http URL 连接超时

[`HttpURLConnection`](https://docs.oracle.com/javase/7/docs/api/java/net/HttpURLConnection.html) 从 JDK 1.1 开始可用，在 JDK 5 版本中获得了网络通信超时的能力。两个可用的超时 [`setConnectionTimeout`](https://docs.oracle.com/javase/7/docs/api/java/net/URLConnection.html#setConnectTimeout(int)) 、 [`setReadTimeout`](https://docs.oracle.com/javase/7/docs/api/java/net/URLConnection.html#setReadTimeout(int)) 分别控制连接建立前的等待时间和等待服务器数据的时间。默认值是**无限‼️** 。

## Apache HttpClient 超时

来自 Apache HttpComponents suite 的 HttpClient 已经成为 http 通信的标准选择。这是一个成熟的项目，丰富的 API 弥补了许多缺点，例如连接池。许多 API 已被弃用，例如`DefaultHttpClient`、`org.apache.http.params.CoreConnectionPNames`，因此在设置超时时需要小心，它们会退回到系统定义的套接字级别默认值。

共有 3 种超时设置:

```
val requestConfig = RequestConfig.custom()
    // Determines the timeout in milliseconds until a connection is established.
    .setConnectTimeout(5_000) 
    // Defines the socket timeout in milliseconds,
    // which is the timeout for waiting for data or, put differently,
    // a maximum period inactivity between two consecutive data packets).
    .setSocketTimeout(5_000)
    // Returns the timeout in milliseconds used when requesting a connection
    // from the connection manager.
    .setConnectionRequestTimeout(2_000)
    .build() 
```

Enter fullscreen mode Exit fullscreen mode

`requestConfig`可以进一步用作`HttpClient`实例的默认值:

```
val httpClient = HttpClients.custom()
    .setDefaultRequestConfig(requestConfig)
    .build() 
```

Enter fullscreen mode Exit fullscreen mode

也可以单独配置每个请求:

```
val get = HttpGet("http://httpbin.org/get").apply { 
    config = requestConfig
}
httpClient.execute(get) 
```

Enter fullscreen mode Exit fullscreen mode

## OkHttp

[OkHttp](http://square.github.io/okhttp/) 是我最喜欢的 Android 和 Java 应用的 HTTP & HTTP/2 客户端。它是高效的，并且具有**良好的配置默认值**。有 3 种超时设置可用:

```
val client = OkHttpClient.Builder()
    // Sets the default connect timeout for new connections.
    .connectTimeout(5, TimeUnit.SECONDS)
    // Sets the default read timeout for new connections.
    .readTimeout(10, TimeUnit.SECONDS)
    // Sets the default write timeout for new connections.
    .writeTimeout(20, TimeUnit.SECONDS)
    .build() 
```

Enter fullscreen mode Exit fullscreen mode

所有`connectTimeout`、`readTimeout`、`writeTimeout`默认为 **10 秒**👍。

## XMLHttpRequest 和 Fetch API 超时

`XMLHttpRequest`是十多年来 Web 应用程序网络通信的标准基础。如今，它正被 Fetch API 所取代，但在未来几年内，它仍将是最受欢迎的。 [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/timeout) 中只有一个`timeout`配置可用:

> XMLHttpRequest.timeout 属性是一个无符号 long 类型的值，表示请求在自动终止之前可以花费的毫秒数。默认值为 0，这意味着没有超时。

**默认为无限‼️**

由于没有配置默认值，我们应该努力在代码中设置超时！与服务器端的超时相比，客户端的超时可能并不那么重要。至少可以说，这是一种值得怀疑的态度。我们需要记住，一个浏览器对一个域的连接数有一个硬性限制，如果我们使用 **HTTP 1，这一点非常重要。** *。当我们达到并发打开连接的最大数量时，任何新的`XMLHttpRequest`都将无限期地排队。这个限制值因浏览器而异，最近的 RCF 放宽了这个限制。**HTTP/2* *缓解了连接多路复用的问题[，尽管如此，其采用率仍然很低。据 w3techs 报道，截止到今天](http://qnimate.com/what-is-multiplexing-in-http2/)，这一比例约为 20%。在单页面应用程序中，`XMLHttpRequest`中使用的超时值甚至更重要。在 SPAs 中，只要服务器和中间网络方允许有效地阻止所有后续网络调用，没有超时的`XMLHttpRequest`就可以存在。

[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 意在替代`XMLHttpRequest`。因此，令人遗憾的是，超时请求的能力还没有成为[的标准](https://fetch.spec.whatwg.org/)。**目前没有强制超时**的标准方法。有几个 GitHub 问题处于活动状态:[添加超时选项](https://github.com/whatwg/fetch/issues/20#issuecomment-323740783) , [添加在经过一定时间后自动拒绝获取承诺的选项](https://github.com/whatwg/fetch/issues/179)检查潜在的解决方案。有一个关于[可取消承诺](https://github.com/tc39/proposal-cancelable-promises)的提案，在[大量讨论和缺乏共识](https://github.com/tc39/proposal-cancelable-promises/issues/70)后被撤回。Edge 和 Firefox 最近实现了一种全新的方式[,允许超时获取 API 调用🎉通过](https://developers.google.com/web/updates/2017/09/abortable-fetch)[将 DOM 标准化`AbortController`](https://dom.spec.whatwg.org/#aborting-ongoing-activities) 。希望它能很快进入 Fetch API 标准。

```
const controller = new AbortController();
const signal = controller.signal;

setTimeout(() => controller.abort(), 5000);

fetch(url, { signal }).then(response => {
  return response.text();
}).then(text => {
  console.log(text);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## URLSession 超时

[`URLSession`](https://developer.apple.com/documentation/foundation/urlsession) 是`NSURLConnection`的继承者，它是大多数(如果不是全部)iOS http 客户端的基础，例如 Alamofire。有两个主要的超时值需要配置，这两个值都有默认值，可通过`URLSessionConfiguration.default` :
获得

```
let sessionConfig = URLSessionConfiguration.default
sessionConfig.timeoutIntervalForRequest = 20.0
sessionConfig.timeoutIntervalForResource = 40.0

let session = URLSession(configuration: sessionConfig) 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，配置了默认值:

*   `timeoutIntervalForRequest`:

    > 此属性根据此配置确定会话中所有任务的请求超时间隔。请求超时间隔控制任务在放弃之前等待额外数据到达的时间(以秒为单位)。每当新数据到达时，与该值相关联的定时器被重置。
    > 默认值为 60。

*   `timeoutIntervalForResource`:

    > 此属性根据此配置确定会话中所有任务的资源超时间隔。资源超时间隔控制在放弃之前等待整个资源传输的时间(秒)。
    > 默认值为 7 天。

注意`timeoutIntervalForResource`是比我们在其他 HTTP 客户端中考虑的更高级别的超时。它包括重试和/或请求超时，因此有一个很大的默认值。

## 总结

许多 HTTP 客户端没有良好的默认超时配置。因此，如果您关心您的应用程序资源使用和系统稳定性，您必须仔细检查和配置适用的超时。令人欣慰的是，现代 HTTP 客户端(如 OkHttp 和 URLSession)有一个简短但合理的缺省值。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)