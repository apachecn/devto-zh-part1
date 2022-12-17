# 恶人不得安宁

> 原文：<https://dev.to/mogui/no-rest-for-the-wicked-e8l>

这篇文章(也读作 *rant* )深受阅读[这篇文章](https://mmikowski.github.io/the_lie/)的启发，它用文字表达了这些年来在我脑海中浮动的一些东西，让服务彼此之间进行交流，无论是 API、移动客户端还是其他什么。

> 为什么我们他妈的到处都在用 *REST~ish* API？

<figure>[![if you dont get this, we cannot talk about music...](img/ebda27af30f1ee988e7fa608f5208bc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z-5D4YBo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/0%2AxlUf1WFioivefp16.) 

<figcaption>如果你不明白这一点，我们就无法谈论音乐……</figcaption>

</figure>

#### 问题

似乎现在一个体面的开发人员必须知道并使用 REST，但是我们是如何做的呢？

对我来说，实现它或者只是消费它是非常清楚的，有很多错误。

开头引用的那篇文章涵盖了主要观点，但我只对其中的一些观点进行评论:

#### 1。" RESTful APIs 通常绑定在 HTTP 上"

并利用它分散了太多的逻辑:标题、动词、uri、响应代码和正文。这使得调试变得困难，而且完全不直观，导致随机实现。

当你想使用第三方 RESTxxx API 时，你**必须**阅读文档，因为你不能想当然地给出任何东西:

*   我需要在查询字符串中提供一个头或一个令牌来认证调用吗？
*   表头是如何命名的？
*   要创建资源，我应该使用 POST 还是 PUT？
*   最新消息呢。
*   对于一个 upsert？
*   出现错误时，我是只检查状态代码还是还要检查有效载荷？(有多少 API 在有效负载中有“status”字段？我自己也做过，很多次)
*   错误信息是什么？
*   要过滤或查询资源列表，我应该在查询字符串中使用哪些字段？
*   如果我想要的不是普通的资源，而是混合的集合呢？

#### 2。“其余词汇不够丰富”

这是千真万确的，因为 REST 是在*资源*、 *CRUD* 操作并链接它们的概念之后设计的。这是一个很好的模型来表示链接的数据并像在 RDBMS 上一样管理它，但是 API 通常不仅仅是 T4。

让我们设想一个移动社交应用程序 API，在其中，您必须:

*   注册用户-> *放、贴用户资源？*
*   认证用户-> *是资源吗？？？*
*   创建新的博客条目-> *这个我知道！！* *帖子/博客 yeh！！完成了。休息太棒了！*
*   喜欢一个帖子-> *mmm 可能是* *POST /blog/666，但是我没有有效载荷！我可以用 GET 吗？这个动作真的创建了一个“like”资源还是仅仅是博客条目资源上的一个标志？我应该使用 PUT 来更新博客条目资源吗？… wtf*
*   搜索资源集合-> *GET /resource yes！搜索标准和过滤器？queryString 参数？车身有效载荷？如果我需要一个帖子，但我没有创造任何东西:(他妈的肥皂不是更好吗？绝对不会。但是 wtf！*
*   询问一个用户的个人资料，但有更多或更少的信息-> *查询字符串参数？另一个 uri 端点？*
*   结交新朋友请求-> *ooh fuck！*

[![](img/0829942a0a768c76312d852f2584803d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zBXyKhOU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/400/0%2AHlVw0Hly7m8727AV.gif)

这是一个混乱，每个人都在以不同的方式做这件事，其他相关的框架也有他们自己的理念。

休息是一种祝福，因为它让我们从肥皂的束缚中解脱出来，但代价是什么呢？

我猜是困惑。

每当我们必须设计一个 REST-ish API 时，我们必须回答太多的问题，问题是**没有正确的答案**。

#### 已有解决方案

显然我不是唯一一个有这些感觉的人，所以周围有人试图解决这些问题，引用文章的作者提出了他的解决方案 [JSON-pure API](https://mmikowski.github.io/json-pure/) 。我认为它不是很漂亮和有用，有点像回到了一个类似肥皂的东西，但是它有很棒的想法:

*   不知道传输，没有更多重要的内容附加到协议特定的东西，如响应代码和头部，使我们能够使用我们应该需要的任何传输:HTTP，Websocket，XMPP …
*   JSON 很容易调试

更稳定和被采用的东西在那里: [jsonapi](http://jsonapi.org/) 、[哈尔](http://stateless.co/hal_specification.html)、[梅森](https://github.com/JornWildt/Mason)、[集合 json](https://github.com/collection-json/spec) 。我认为每一个都不是完整的，或者只是以不同的方式处理相同的资源/CRUD 模型。

我还认为:我们需要一种*标准*来做*标准的事情*。

SOAP 虽然不好，但它让你自己创建客户端，这可能太多了，但它使用起来很直观，其他东西则不然。

不，我不想回到 SOAP :P 所以…

#### 一个想法…

新思想是旧思想。

我不是说要写一个新的协议规范。

<figure>[![xkcd](img/7e6debebfc5d0313396abe2ad4027450.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vqRH1fra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/0%2AA-KwNlmdkUdMlKfo.png)

<figcaption>xkcd</figcaption>

T6】</figure>

我认为正确的东西已经存在，但只是需要放在一起。

所以我想写的更多的是一个“最佳实践的规范”或者一个“只是已知的协议组合的协议”。

#### 它应该由什么组成？

*   既然 API 不仅仅是 CRUD 操作，而是更多类似于*动作*或*过程*的东西，也许一个简单的 **RPC** 就是我们所需要的。我们已经有了一个很好的规范: [JSON-RPC](http://www.jsonrpc.org/specification) 。它很干净，像 json 一样简单，与传输无关，而且已经有很多客户端库了。
*   请求认证，在需要的地方，应该被嵌入到这个协议中，我认为**是合适的。关于认证的 RPC 方法也应该在协议规范中标准化 jwt 的生成。**
***   我们可以做一个冒险的跳跃，在 JSON-RPC 有效负载上使用 [JSON Schema](http://json-schema.org) 来验证 API 格式，并且毫不费力地从 Schema 中自动生成 API 客户端库(hello again SOAP :3)。**

 **#### 你确定你需要 RPC 吗有个东西叫 GraphQL …

我记下这些关于休息的想法已经有一段时间了，但是从来没有进行过有机的讨论，看来我等得太久了。我并不是昨天偶然发现了 GraphQL，对它只是略知一二，我认为它是一种检索数据的好方法。近距离的观察让我完全重新考虑 RPC。有了*突变*你可以实现任何事情，这真的就像一个 RPC 提前声明你想要的结果，很美。RPC 所缺少的是响应中统一的错误处理，GraphQL 在这方面没有强制任何东西。与 RPC 不同的是，GraphQL 也不处理认证，所以你必须自己处理，谁说的 JWT？？。

我们可以在查询/变异中嵌入两者，类似于:

```
mutation {
  login(username: String!, password: String!) {
    token
    error
  }
}

query {
    myUltraAwesomeApi(token: String, aParameter: String) {
        error,
        result
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 结论

是时候让 REST 安息了，通过结合这些在很多情况下确实更合适的旧/新技术来制作 API。所以出去玩玩 **RPC** 、 **GraphQL** 和 **JWT** ！

* * ***