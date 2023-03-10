# 缩放 React 服务器端渲染

> 原文：<https://dev.to/arkwright/scaling-react-server-side-rendering-3hl>

*想要的阅读体验，参见本文的[原文。](http://arkwright.github.io/scaling-react-server-side-rendering.html)*

[![Giant stack of React service instances in the shape of a shoddy pyramid, with the top one falling off, possibly to crush a tiny developer below. 8/10 pretty good metaphor.](img/4af51ec35c0ec998d0f007d444a1ade2.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/scaling-react.svg)

# 缩放 React 服务器端渲染

我有机会扩展 React 渲染服务，调整固定的硬件供应以应对不断增加的负载。在几个月的时间里，对该系统进行了逐步改进，使其能够满足需求。我认为分享我在这个过程中获得的更有趣的见解可能是有用的。

这里的一些见解是特定于 React 的，但许多只是一般的可伸缩性挑战，或者是犯下的简单错误。React 服务器端性能优化已经在别处讨论过了，所以我一般不打算提供 React 性能的概述。我将把重点放在我们享受的“重大胜利”上，以及微妙、迷人的[足球赛](https://en.wiktionary.org/wiki/footgun)。我的希望是，除了设置`NODE_ENV=production`的标准建议之外，我可以给你一些有趣的东西来思考。这是基于我们必须克服的真正的挑战。

我发现这个项目最有趣的地方在于调查线索的走向。我认为提高 React 服务器端的性能可以归结为正确实现许多特定于 React 的最佳实践。后来我才意识到，我在错误的地方寻找表现。幸运的话，这些故事将使您能够诊断或避免您自己的性能陷阱！

[![Stick figure swinging on a rope over a bottomless pit, towards a shimmering React logo. Remember Pitfall?](img/b744c21d3dbe8db45083e9ea1ef461f6.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/pitfall.svg)

## 形势

我们的团队希望振兴我们产品的前端架构。就像很多年前的一个庞然大物一样，技术债务堆积如山，前端修改变得越来越困难。我们越来越多地告诉产品经理，他们要求的变更是不可行的。是时候认真对待可持续性了。

在前端团队中，很快达成了共识，即基于 React 和 Redux 的面向组件的架构是可持续未来的最佳选择。我们的集体经验和直觉倾向于在组件级别分离关注点，尽可能提取可重用的组件，并拥抱函数式编程。

[![It is surprisingly hard to draw the Redux logo! (Along with three very poorly drawn Redux logos.)](img/bc81eaa8b63ec1473bc6740d1b5a6389.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/redux-logo.svg)

我们从最普通的前端开始，大多数单片应用程序似乎都进化成了这种前端。浏览器请求会碰到负载平衡器，它会将请求转发给 Java/Spring monolith 的几个实例之一。JSP 生成的 HTML 模板被返回，样式为 CSS (LESS ),动态客户端功能被附加了大量的 jQuery。

[![Diagram of monocled, top-hatted user connecting to a Load Balancer, which forwards the request to a Monolith. Monolith responds by returning a rendered JSP document to the load balancer, which sends it to the user. Pretty boring stuff.](img/a72d609bd8b2fec392393d917e0aac6e.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/starting-architecture.svg)

问题是如何将我们对 React 前端的渴望与 Java monolith 整合起来。SEO 是一个非常重要的考虑因素——我们有全职的 SEO 顾问——我们希望提供尽可能快的页面加载速度，因此服务器端渲染很快成为一个需求。我们知道 React 能够进行同构(客户端和服务器端)渲染。后端团队已经踏上了将整体结构分解为微服务架构的征程。因此，将我们的 React 服务器端呈现提取到自己的 Node.js 服务中似乎是再自然不过的事情了。

[![Diagram of our MacGuffin, the monocled, top-hatted user, connecting once again to the Monolith via the Load Balancer. This time, Monolith requests some React component renders from the React service, which sends a response containing the rendered components, a serialized Redux store, and mounting instructions. The Monolith takes these pieces and merges them into a JSP, sending the final output through the Load Balancer and back to the user. Slightly less boring stuff.](img/5584b23d6fc355f9ef09b96bf5c89d7b.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/final-architecture.svg)

想法是 monolith 将继续呈现 JSP 模板，但是将页面的某些部分委托给 React 服务。monolith 将向 React 服务发送呈现请求，包括要呈现的组件的名称，以及组件需要的任何数据。React 服务将呈现所请求的组件，将可嵌入的 HTML、React 安装指令和序列化的 Redux 存储返回给 monolith。最后，monolith 会将这些资产插入到最终呈现的模板中。在浏览器中，React 将处理任何动态重新渲染。其结果是在客户端和服务器端呈现单一的代码库——这是对现状的巨大改进。

随着我们对这种新方法越来越有信心，我们将使用 React 构建越来越多的功能，最终将整个页面呈现委托给 React 服务。这种方法允许我们安全地、增量地迁移，避免了大爆炸式的重写。

[![The same web page, composed three different ways, demonstrating an incremental migration path. Starting form is entirely JSP-rendered. Transition form contains a mixture of JSP and React-rendered elements. Goal form is one huge React-rendered component.](img/42dad9065442b5877d2839c95cec7c56.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/starting-transition-goal.svg)

我们的服务将作为 Docker 容器部署在 Mesos/Marathon 基础设施中。由于极其复杂和枯燥的内部动态，我们没有太多的横向扩展能力。我们无法为群集配置额外的机器。我们的 React 服务仅限于大约 100 个实例。它不会总是这样，但在向同构渲染过渡期间，我们必须找到一种在这些约束内工作的方法。

## 负载均衡

### 我得到了 99 个百分点

这种转变的最初阶段并不是没有障碍，但是我们的 React 服务呈现性能是合理的。

[![Graph of Response Latency (ms) over time. p50 response time is plotted as being fairly consistent at around 5ms. p99 response time is more erratic but generally around 50ms.](img/0887d0b6c957c5c6e88772df5aab83d1.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/response-latency-5-50ms.svg)

随着我们将网站越来越多的部分移植到 React，我们注意到我们的渲染时间在增加——这是意料之中的——但我们的第 99 个百分点尤其惊人。

[![Graph of Response Latency (ms) over time. p50 response time is plotted as being fairly consistent around 30ms. p99 response time is fairly erratic but generally around 250ms. Yeah, I know, graphs are boring. Drawing them was surprisingly fun, though. At least one of us is having fun.](img/2400e99710c0a7f44fb47d9cd53748c5.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/response-latency-30-250ms.svg)

更糟糕的是，当我们的流量在晚上达到峰值时，我们会看到 99%的响应时间出现大峰值。

[![Graph of Response Latency (ms) over the course of about 12 hours, from 12pm to 12am. p50 response time is plotted as being fairly consistent at around 30ms, and increases to about 50ms in the evening, starting at about 6pm. p99 response time is more erratic, hovering around 250ms, and increases to around 350ms in the evening, also starting around 6pm. By 12am response times are clearly decreasing again. If the previous graphs were boring, this graph is like the beginning of an episode of your favourite TV crime drama.](img/34f2708b450d66c1d0d922dd41c58a12.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/response-latency-evening.svg)

从我们的基准测试中我们知道，在 React 中渲染一个相当复杂的页面根本不需要 400 毫秒。我们对服务的渲染效率进行了分析和大量改进，包括流响应、将组件元素重构为 DOM 节点元素、各种 Webpack 诡计，以及为一些组件引入缓存渲染。这些措施缓解了问题，有一段时间我们徘徊在可接受性能的边缘。

### 季节性

一天，我正在查看我们的响应延迟图，我注意到问题又出现了。前一天晚上异常高的流量使得我们的 99%响应时间超过了可接受的阈值。我对此不屑一顾，认为这是个例外——我们忙得不可开交，我没有时间去调查。

这种趋势持续了几天。每天晚上交通高峰时，我们都会创造新的记录。缩小以显示最近几天，响应时间有明显增加的趋势。

[![Graph of Response Latency (ms) from Monday to Friday. p50 response time is plotted as being fairly consistent at around 50ms, though daily peaks and troughs are clear. p99 response time is more erratic at around 250ms, also with daily peaks and troughs. Importantly, the peak p99 response time is increasing with each passing day, and approaches 400ms on Friday. Seeing something like this in real, production graphs makes you feel like a cast member in Armageddon. Now you can't get that Aerosmith song out of your head. Look, I didn't say you would *like* the alt text.](img/116bcc240747696096b3e70296921eae.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/response-latency-week.svg)

在交通流量和响应时间的图表中有明显的相关性。我们可以尝试用胶带来解决这个问题，但如果交通流量增加，我们的处境将会很糟糕。我们需要横向扩展，但是我们做不到。那么我们离灾难有多近呢？我拿出一张年度交通图，迅速吐出我的茶。

[![Graph of Requests Per Minute over the course of one year, from January to the end of December. Y-axis goes from zero to 'lots' of requests. A single line plots requests per minute, which vary a bit through the day, so the line is slightly erratic. Still, a general trend is clear, with requests hitting a low point in December, and increasing until a high point in July. A 'You are here.' arrow points to early March, which is where I was situated when I first looked at this graph (and promptly spit out my tea).](img/b783ec721854e945302ed9dcb4c4cf9a.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/requests-per-minute-annual.svg)

毫无疑问，我们的响应时间会随着流量的增加而显著增加。现在是春天——大约是一年中流量的中点——到了夏天，我们就会被请求淹没。这非常糟糕。

但是我们怎么会错过这个呢？我们以为我们已经解决了这个问题。怎么回事？

我很确定，由于交通的季节性，我们措手不及。从去年夏天开始，当流量达到高峰时，我们开始移动越来越多的功能来应对。如果流量保持不变，增加的组件渲染负载会导致我们的响应时间增加。相反，随着时间的推移，交通流量在减少。请求在减少，但是每个请求的工作负载在增加！结果是，在秋季和冬季，响应时间大致持平。随着流量在春季再次增加，我们的响应时间迅速增加，这一次，每个请求的工作负载的增加放大了这一影响。

[![Graph of Response Latency (ms) vs # React components. X-axis spans one year, from July to July. Y-axis ranges from 0 to 400 ms response latency. Two lines are plotted: p99 response latency and number of components. Both lines suddenly begin in mid-July. Response latency starts around 100ms (hey, this graph isn't exactly to scale, I'm drawing it from memory), and increases to around 300ms in December, then decreases during the winter, to about 250ms in February, finally increasing roughly linearly to a peak of 400ms in July. The number of components line increases purely linearly from mid-July to the following July, though I didn't provide a value for its Y-axis, simply showing that it starts from a small number of components, and increases to 'lots'. The general idea of this graph is that the number of components can increase while response times decrease, because demand for rendering can fluctuate seasonally. I think this is the biggest alt text I've ever written. In a pinch, you could use this alt text to filibuster.](img/dcf37780072c52790415f22cd898f05d.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/response-latency-vs-components.svg)

### 随机性

出于从系统中挤出简单绩效的想法，我开始向我的一些同事寻求建议。在其中一次对话中，有人提到我们的服务发现机制 Consul 为每个服务发现请求返回三个随机的服务实例。

我记得几年前读过一篇[神奇的 Genius 文章](https://genius.com/James-somers-herokus-ugly-secret-annotated)，其中讲述了当 Heroku 悄悄地切换到随机负载平衡策略时，他们经历的性能退化，导致扩展效率下降了 50 倍。如果我们使用类似的负载平衡策略，那么我们可能会遭受同样的命运。我做了一些研究，证实了这确实是事实。

基本上，当 monolith 需要向 React 服务发出请求时，它需要知道 IP 地址和端口，以便定位该服务的实例。为了获得这些信息，向 Consul 发送一个 DNS 请求，Consul 跟踪每个活动的服务实例。在我们的配置中，对于每个服务发现请求，Consul 从池中返回三个随机的实例。这是系统中唯一的负载平衡机制。呀！

[![Diagram of how service discovery works. Monolith makes a call to Consul requesting the location of the React service. Consul responds with three different IP addresses and port number combinations, which are the locations of three random React service instances. The Monolith then sends a request to one of these React service instances, asking for a Header component to be rendered. The React service renders the component, and returns it to the Monolith.](img/aad5286425795874666f02ec632c7555.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/service-discovery.svg)

在我继续之前，我应该解释一下为什么随机负载平衡是低效的。

假设您有一个负载平衡器和三个服务实例。如果负载均衡器随机地将请求*路由到这些实例，请求的分布将会非常不均匀。*

[![Diagram showing Load Balancer routing requests randomly to three service instances. Instances receive 7, 2, and 4 requests, respectively. Random load balancing always produces a distribution like this. Yes, it does. YES IT DOES!!!1](img/bfd4e61a9dd68b3768fb35071f58656b.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/random-load-balancing.svg)

我已经向很多人解释过这个问题，它让很多人感到困惑。这让我想起了[蒙蒂霍尔问题](https://en.wikipedia.org/wiki/Monty_Hall_problem)——即使这是真的，人们也很难相信。

但是没错，这是真的:随机负载均衡根本没有均衡负载！如果你抛一枚硬币，数一数正面和反面，这就更容易理解了。这种平衡几乎总是不平衡的。

[![Diagram of coin tosses over time, titled, 'Coin tosses are random yet uneven!' Describing this is going to be tricky, since I had to invent this visualization to make the point, but here we go. The x-axis represents time, and the y-axis represents bias in coin tosses towards heads or tails. With each coin toss, the plotted line moves incrementally upwards (towards heads), or downwards (towards tails). Each point on the line is marked with an 'H' or a 'T', respectively. When coin tosses are evenly distributed, the line remains roughly horizontal, moving up, down, up, down, etc. When coin tosses are uneven, such as when we get four heads in a row towards the end of the graph, the line moves up, up, up, up. This diagram shows that, at any given point in time, coin toss results can be unbalanced in favor of heads or tails, despite the long-term trend being towards evenness. These were actual coin tosses that I performed at like 1am, when I was suddenly inspired to draw this diagram. That's how committed I am to you. I'm here for you.](img/1b56fb884facbb50cef9d5c9f98c7495.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/coin-tosses.svg)

一个常见的反应是，负载在开始时可能不均衡，但随着时间的推移，负载将“平均化”，这样每个实例将处理相同数量的请求。这是正确的，但不幸的是，它忽略了一点:几乎在每一个*时刻*，负载会不均匀地分布在各个实例上。事实上，在任何时候，一些服务器都会比其他服务器并发处理更多的请求。当服务器决定如何处理这些额外的请求时，问题就出现了。

当服务器负载过重时，它有几个选择。一种选择是丢弃多余的请求，这样一些客户端将不会收到响应，这种策略被称为*减载*。另一种选择是对请求进行排队，这样每个客户端都会收到一个响应，但是这个响应可能需要很长时间，因为它必须在队列中等待。老实说，这两种选择都是不可接受的。

[![Diagram of how load shedding works. Load Balancer sends 3 requests ('too many requests!') to an instance of a service. The instance sends a response for request #1, and and discards requests #2 and #3 into a bottomless pit, which is actually how servers work and not at all a metaphor.](img/8b1a3e26b314506978139758960110c1.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/load-shedding.svg)

[![Diagram of how queuing works. Load Balancer sends 3 requests ('too many requests!') to an instance of a service. The instance sends a response for request #1, and enqueues the remaining two requests for later processing. I drew tiny front and back doors on the service, which the queued requests can use to enter and exit. One cannot live on boxes and arrows alone.](img/06e070dc3a61ad96c1d90de821ad5349.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/queueing.svg)

我们的节点服务器正在排队过多的请求。如果每个并发请求至少有一个服务实例，那么每个实例的队列长度将始终为零，响应时间将是正常的，前提是我们均衡地平衡负载。但是当我们使用随机负载平衡策略时，一些实例将*总是*接收不公平份额的请求，迫使它们将多余的请求排队。队列后面的请求必须等待整个*队列被处理，这大大增加了它们的响应时间。*

[![Diagram entitled 'Queues increase response time!' Load Balancer sends 3 requests ('too many requests!') to an instance of a service. The instance sends a response for request #1, and enqueues the remaining two requests for later processing. Each requests requires 10ms to process, so request #1 experiences 10ms latency. Request #2 must wait for request #1's 10ms processing, and then also its own, suffering 20ms latency in total. Request #3 accordingly must wait for 30ms. This cycle continues until either all requests are handled or your server explodes, all of your users defect, and your team disbands.](img/aa1cd94d6b05f88e720a359617f8a682.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/queues-increase-response-time.svg)

更糟糕的是，我们有多少服务实例并不重要。请求的随机分配保证了一些实例将总是处于空闲状态，而另一些实例则被过多的流量压垮。添加更多的实例会降低多个请求被路由到同一个实例的可能性，但并不能消除这种可能性。要真正解决这个问题，您需要负载平衡。

我安装了度量工具来绘制每个服务实例的请求队列长度，很明显，一些服务比其他服务排队更多的请求。随着时间的推移，分布会发生变化，因为随机负载平衡只是碰巧选择了不同的实例。

[![Graph of Request Queue Length (Per Instance). X-axis represents time, and y-axis spans from 0 to 5 requests enqueued. Three hypothetical service instances are plotted, with queue lengths that vary from 0 to 3 requests. Crucially: the queue lengths for each service are different, and constantly fluctuating, a symptom of uneven load balancing.](img/60bf70b606395b93c331e8300af02d28.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/instance-queue-length.svg)

### 负载均衡策略

因此，我们需要确保负载在实例间均匀分布。我不想重复过去的错误，于是开始研究负载平衡策略。这是一个非常迷人的话题，如果你有兴趣了解更多，我强烈推荐泰勒·麦克马伦的演讲， *[负载平衡是不可能的](https://www.infoq.com/presentations/load-balancing)* 。

不幸的是，负载平衡策略的排列如此之多，以至于不可能在生产环境中测试所有的策略。每种策略的迭代成本都太大了。因此，我遵循 Genius 的指导，编写了一个简单的内存负载平衡模拟器，使我能够在几个小时内试验几十种策略。这让我对将在生产中测试的解决方案的候选名单更有信心。

### 随机重试减载

一个聪明的解决方案是配置我们的 React 服务来减少负载，返回一个`503 Service Unavailable`而不是排队过多的请求。monolith 或多或少会立即接收到`503`,然后在一个不同的随机选择的节点上重试它的请求。每次重试到达另一个过载实例的概率呈指数递减。

[![Diagram of how load shedding can be used in conjunction with random retries to achieve a kind of load balancing. Monolith sends a request to an instance of a service, which responds with a 503 Service Unavailable, because it has too many requests in its queue. Monolith then retries its requests on a different, randomly selected instance. The second instance does not have a queue and so it happily responds with a cool, sunglasses emoji, or whatever other metaphor you want to use for a successful response. If you had requested a poop emoji, the response would be a poop emoji, but then it seem look like a failed response, so I went with sunglasses emoji instead.](img/4b25c7b9e098b017a9824d141f00e151.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/load-shedding-503.svg)

不幸的是，当我模拟这种方法时，我发现它不是最有效的。这当然比单次随机尝试要好，但是它的表现不如循环算法。

这有几个原因。首先，每次重试都会增加最终响应时间的网络延迟。在所有其他条件相同的情况下，不发出冗余请求的算法不会遭受这种开销。

[![Diagram of how load shedding with random retries adds latency with each unsuccessful retry. Assumem 5ms latency for every request or response sent. Monolith sends a request to an instance of a service, which responds with a 503 Service Unavailable. Monolith then retries on a different instance, which responds successfully. The total network cost is 20ms, instead of the 10ms you would have suffered if there had been no retry. Neutral face emoji demonstrates the unsatisfactory delay. Really, you only need a couple of emojis to handle most situations. Smiley, frowny, neutral, poop, sunglasses. I guess poop emoji is usually smiling, but that's a technicality. Definitely the person looking back at poop is not smiling. Well, I guess that also depends. It's complicated. Let's move on.](img/45878e652332768c75b3e03882a32564.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/latency-adds-up.svg)

第二，随着服务实例集群的流量饱和，重试到达健康实例的概率会降低！想象一下一个有 5 个实例的集群，容量为 4 个实例，无法处理额外的请求——重试到达 1 个可用实例的几率只有 20%!这意味着一些请求将遭受许多次重试才能收到响应。

[![Diagram showing Monolith choosing randomly among 5 service instances for its next request. Instances have 3, 2, 1, 0, and 4 requests in queue, respectively. Random selection has a 20% chance of picking the instance with no requests in its queue, i.e. the 'best' instance. Of course, I love all my instances equally. None of them are truly the 'best'. Some are just more enqueued than others.](img/24d0c6b61ab582c4899711ea03a80e8c.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/probability.svg)

当您可以水平扩展时，这个问题就不那么明显了，但是希望这种解决方案的低效是显而易见的。如果可能的话，我想做得更好。

### 循环赛

一个更好的方法是将每个请求依次路由到集群中的下一个实例，通常称为*循环*算法。

[![Diagram of how round-robin load balancing works. Load balancer sends 9 requests to 3 service instances. Instance #1 receives requests 1, 4, and 7\. Instance #2 receives requests 2, 5, and 8\. Instance #3 receives requests 3, 6, and 9\. Round-robin simply picks the next instance, in order, and sends a request to it. The result is an even distribution of requests across instances. I don't know why it's called round-robin, though. It doesn't have anything to do with robins. Or even Batman & Robin, which was mediocre at best. George Clooney is too pretty to be Batman.](img/23b7ea8eb70e96346085f557f06ff7d6.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/round-robin.svg)

循环调度保证了每个服务实例将准确地接收其公平份额的请求。这是最简单的负载平衡算法，我们可以诚实地说是以一种有意义的方式平衡负载。因此，它大大优于随机重试的随机减载。

表面上看，循环调度并不是绝对最有效的方法，因为请求需要服务器执行的工作量会有所不同。一个请求可能需要 5 毫秒来呈现单个 React 组件，而另一个请求可能需要 50 毫秒来呈现充满数百个组件的页面。每个请求工作负载的这种自然差异意味着循环调度可以向仍在处理前一个请求的实例发送请求，而其他实例保持空闲。这是因为循环调度没有考虑实例的工作负载。它*严格地*分配请求，就像 21 点庄家发牌一样:每个人都拿到相同数量的牌，但是有些牌比其他的好！

[![Comic of a blackjack dealer talking to a single player. 'Dealer has 20\. You have... 96'. Caption underneath reads, 'Distributed systems blackjack.' I really messed up the bow tie on the dealer, but I'm too lazy to draw it again.](img/563120eb494580cba7f9b2b499bd9db6.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/blackjack.svg)

### 加入最短队列

显然，我们不能谈论“最佳”负载平衡算法，因为“最佳”选择取决于您的特定环境。但是，如果我不描述可能是最广泛有用的方法，即*加入最短队列*策略，那将是我的失职。

我将把这种策略的几种变体放在一起。有时我们可能会使用*最少连接数*，或者*加入空闲队列*的方法，但是统一的原则是相同的:尝试将请求发送到负载最小的实例。我们可以使用不同的试探法来估算“负载”，包括实例队列中的请求数，或者未完成的连接数，或者让每个实例在准备好处理另一个请求时进行自我报告。

[![Diagram of how join-shortest-queue load balancing works. Three service instances have 2, 3, and 1 requests enqueued, respectively. Load Balancer sends the next request to the instance with only 1 request in queue. It is observed that round-robin might pick the other two instances, because round-robin is about as good at load balancing as George Clooney is at playing Batman. Great actor, just not for Batman. Great actor, though.](img/d62ed0b2bf8a69121fc1ed251e41a738.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/join-shortest-queue.svg)

最短队列连接方法优于循环法，因为它试图考虑每个请求的工作负载。它通过跟踪每个实例等待的响应数量来做到这一点。如果一个实例正在努力处理一个巨大的请求，它的队列长度将是 1。与此同时，另一个实例可能会完成它的所有请求，将其队列长度减少到 0，此时负载平衡器会优先向它发送请求。

### 法比奥

那么，我们如何解决我们的负载平衡困境呢？我们最终实现了一个循环负载平衡器， [Fabio](https://github.com/fabiolb/fabio) ，作为一个折衷的解决方案，为了方便而牺牲了性能。

虽然 Fabio 不支持加入最短队列负载平衡策略，但它与 Consul 无缝集成，为我们提供了[服务器端服务发现](http://microservices.io/patterns/server-side-discovery.html)。这意味着我们的 monolith 可以简单地向 Fabio 发送请求，Fabio 计算出如何让这些请求到达 React 服务，以及如何以合理的方式平衡负载。

[![Diagram of how Fabio acts as a load balancer within the architecture. Monolith sends requests to Fabio, which then contacts Consul to get the IP addresses and port numbers of the destination service instances. Fabio then forwards the request to a service instance, using a round-robin algorithm. Service instance sends a response to Fabio, which forwards it back to the Monolith. In reality the Consul service discovery lookups are cached, otherwise too much latency would be introduced. I reserve the right to simplify things for pedagogical purposes. If you don't like it, draw your own diagrams.](img/17707684c7950a17e470142db682d443.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/fabio.svg)

当然，在这种配置中，我们的负载均衡器会成为一个单点故障——如果它停止工作，我们将无法呈现任何网页！

为了提供可用性策略，我们将 Fabio 负载平衡器作为另一种容器化服务来实施——负载平衡即服务。monolith 将使用 Consul 来发现一个随机的 Fabio 实例，并向该实例发送请求。如果 Fabio 实例死亡，Consul 会自动检测到这一点，并停止提供该实例作为随机选项之一。我们在生产中测试了故障转移，方法是通过 Fabio 发送少量流量，然后手动终止 Fabio 实例。Consul 将在几秒钟内可靠地从这个故障中恢复。还不错！

[![Diagram of how multiple Fabio instances provide failover capability. Monolith requests the IP and port of a Fabio instance from Consul. Meanwhile, a Fabio instance has exploded. Consul quickly detects that Fabio has exploded, and does not return that Fabio instance as an option for the Monolith to connect to. The Monolith is therefore unaware of the failure, and blissfully sends its traffic to one of the remaining Fabio instances. Fabio then forwards requests to the React service, which sends responses back to Fabio, which forwards them back to the Monolith.](img/56b7762b2afb32fb619f410f27a420fd.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/fabio-failover.svg)

我们可能会假设随机选择一个负载均衡器会保留我们试图解决的性能问题，但实际上这不是问题。Fabio 的每个实例都可以轻松容纳发往 React 服务集群的所有流量。如果我们的负载均衡器足够快，那么负载均衡器本身的负载是否均衡并不重要。我们有多个负载平衡器，纯粹是为了提供故障转移功能。

### 巨大的成功

当新的循环负载平衡策略被生产出来，并增加到 100%的流量时，我们的 React 服务实例队列长度是引人注目的。所有的队列都集中在相同的长度。系统起作用了！

[![Graph of Request Queue Length (Per Instance). This time, with load balancing installed! X-axis is time, and y-axis is request queue length, from 0 to 5\. Three service instance request queues are plotted, and you know what? They're all sitting steady at 2 requests in queue! Ideally we would have less than 1 request in queue, but the point is that with load balancing finally installed, no instance can become overloaded. Make sure to balance your loads, people.](img/f9bcf7992ad708fb75cc16a4f994aaef.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/queue-length-with-load-balancing.svg)

更好的是，我们最初的问题得到了解决:峰值流量响应延迟峰值变得平稳，我们的 99%延迟下降了。正如我们最初希望的那样，一切都“正常工作”。

[![Graph of Response Latency (ms) during the activation of load balancing. p50 response time is about 50ms, until load balancing is activated, at which point it drops to about 40ms. p99 response time is always erratic, and hovers around 350ms until load balancing is activated, after which it drops to around 200ms. A terrific win!](img/4222e353adb886baf5748ea53db31082.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/latency-with-load-balancing.svg)

## 客户端渲染回退

### 弹性无弹性

向我们的系统添加负载平衡有效地解决了我们的高延迟问题，并且效率的提高提供了适量的额外容量。但是我们仍然担心非常情况。机器人会抓取我们的网站，引发大量的请求。季节性，包括假期，也可能引发交通意外增长。我们有足够的服务器容量来满足正常的流量增长，但是我们只能放心地睡觉，因为我们知道我们的系统在高得多的负载下是有弹性的。

理想情况下，我们将建立一个自动扩展系统，可以检测流量激增，并水平扩展以适应它们。当然，这不是我们的选择。我们也不能简单地调配超过所需容量 10 倍的容量。我们有没有办法增加某种安全系数？事实证明，是有的。

我们不能通过丢弃请求来减少负载，但是我开始更普遍地考虑减少负载，我开始想某种负载*节流*是否可能。一天深夜，我突然想到了一个解决办法。我们使用 Redux，Redux 的一个优点是它使得状态的序列化变得非常容易，支持同构渲染。我们在服务器上渲染请求，然后在客户端处理重新渲染，然而同构渲染允许我们在*上渲染*服务器*或*客户端。我们不必总是两者都做。

因此，降低负载的方法非常简单:当服务器处于高负载时，跳过服务器端渲染，并强制浏览器执行初始渲染。在非常需要的时候，我们的渲染能力会自动扩展到包括每个用户的计算机。我们会牺牲一点页面加载速度来换取在固定数量的硬件上进行弹性伸缩的能力。Redux 是不断给予的礼物！

[![Diagram of how React server-side rendering works. Browser sends a request to the Monolith, which requests some React component renders from the React service. The React service responds with the rendered components, serialized Redux store, and mounting instructions. These pieces are merged into a JSP template by the Monolith, and sent back to the browser. Pretty straightforward.](img/ea293c1cf537da392865d9808876184f.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/server-side-rendering.svg)

[![Diagram of how client-side rendering fallback works. Browser sends a request to the Monolith, which requests some React component renders from the React service. This time, the React service is under heavy load and skips the React component rendering, responding only with a serialized Redux store, and mounting instructions. These pieces are merged into a JSP template by the Monolith, and sent back to the browser. When React mounts in the browser, it performs an initial render, and the user can finally see a picture of a cat, or whatever they were looking for. That's really none of our business. We are in the React and Redux businesses, respectively.](img/7c34f97fcd77b1f51ac3a2a55fdfdcb6.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/client-side-rendering-fallback.svg)

### 工作原理

构建客户端呈现回退系统非常简单。

节点服务器只是维护一个请求队列长度计数器。对于收到的每个请求，递增计数器，对于发送的每个错误或响应，递减计数器。当队列长度小于或等于`n`时，执行常规数据提取、Redux 存储水合和服务器端 React 渲染。当队列长度大于`n`时，跳过服务器端 React 渲染部分——浏览器将使用 Redux 存储中的数据来处理。

[![Diagram of how client-side rendering fallback acts as a kind of load throttling. Monolith sends 7 requests to the React service. The first request is server-side rendered, because at that point in time, the service has 0 requests in queue. The next 6 requests are triaged and queued. The first 2 requests are queued for server-side rendering, because we have arbitrarily chosen a queue length of < 3 as our light load cutoff for server-side rendering. The next 3 requests are queued for client-side rendering, because we have arbitrarily chosen a queue length of < 6 as our heavy load cutoff for client-side rendering. The final request exceeds our maximum queue length, and is dropped in order to shed load.](img/ec5920c2a3ea49ab94b9681485055f31.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/client-side-rendering-fallback-queue.svg)

需要调整`n`的精确值，以匹配您的应用的特性。一般来说，`n`应该略大于峰值预期负载时的典型队列长度。

当然，如果 SEO 是一个需求，这种方法包含一个小问题:如果搜索引擎在流量激增时抓取站点，它可能不会收到服务器端呈现的响应，因此它可能不会索引您的页面！幸运的是，这个问题很容易解决:为已知的搜索引擎用户代理字符串提供一个例外。

[![Comic of people lined up to get into Club React. Google made it past the bouncer, who is speaking with the next person in line, who is arguing, 'I'm a friend of Google.'](img/3f5ca3d9dad1314d9ca2e5e380f3a56b.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/club-react.svg)

有一种可能性，搜索引擎将惩罚我们的排名，因为我们对待它的方式不同于其他客户。然而，重要的是要记住，客户端呈现回退的存在是为了防止我们在流量激增或服务器故障时丢弃请求。这是罕见的特殊情况下的安全网。另一种选择是冒险不发送任何东西给爬虫，这也可能导致惩罚。此外，我们没有为搜索引擎提供*不同的*内容，我们只是为它提供优先渲染。很多用户会收到服务器端呈现的响应，但搜索引擎总是会收到一个。当然，如果这种优先权被认为是适得其反的，就很容易取消它。

### 结果

在我们部署客户端渲染回退到生产的第二天，出现了一个流量高峰，结果非常显著。这个系统的表现完全符合我们的期望。我们的 React 服务实例自动开始将呈现委托给浏览器。客户端呈现增加，而服务器端请求延迟大致保持不变。

[![Graph of Requests Per Minute with client-side rendering fallback installed. X-axis is time, and y-axis ranges from 0 to 'lots' of requests. Two lines are plotted: total requests, and client-side rendered requests. Total requests is roughly constant until a big spike occurs, which lasts for a while, and then returns to normal levels. During the spike, client-side renders increase from 0 to a level which mirrors the increase in total requests. When the spike ends, client-side renders return to zero. The system works!](img/1a0295417fbf00d47ba6adeef1e8ebb8.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/client-side-render-requests.svg)

我们对通过这种方法获得的效率进行了基准测试，发现它提供了大约 8 倍的容量增长。在接下来的几个月里，这个系统多次拯救了我们，包括在一次部署错误期间，这大大减少了 React 服务实例的数量。我对结果非常满意，并建议您在自己的同构渲染设置中尝试这种方法。

## 减载

### 为什么需要减载

前面我提到过，减载可以与随机重试结合使用，以提供对纯随机负载平衡的改进。但是，即使使用不同的负载平衡策略，确保 React 服务能够通过丢弃多余的请求来减轻负载仍然很重要。

我们在一次反常的操作事故中艰难地发现了这一点。一个傀儡错误配置意外地重启了集群中每台机器上的 Docker，*同时*。当 Marathon 试图重新启动 React 服务实例时，第一个向 Consul 注册的实例会将 100%的正常请求负载路由给它们。单个实例可能会被其正常请求负载的 100 倍淹没。这非常糟糕，因为实例可能会超出 Docker 容器的内存限制，从而触发容器的死亡。由于一个实例不太活跃，其他实例现在被迫承担额外的负载。如果我们运气不好，可能会发生级联故障，整个集群可能无法启动！

[![Diagram of how services which lack load shedding can experience a cascade failure when starting. Load Balancer sends a firehose of requests to one instance of React service, which enqueues as many as it can until its memory is exhausted and it explodes. Another instance of React service comes online, and the Load Balancer, with nowhere else to send its requests, directs the firehose toward this new instance, which promptly screams 'Crap!' The cycle continues until either you go out of business or somebody installs load shedding.](img/93dd25264eed539313117b63a69890af.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/cascade-failure.svg)

在这个事件中检查我们的图表，我看到一些服务实例的请求队列长度达到了数千个。幸运的是，服务恢复了，我们立即安装了一个负载削减机制，将请求队列长度限制在一个合理的数量。

### 没那么快

不幸的是，节点事件循环使得减载变得棘手。当我们放弃一个请求时，我们希望返回一个`503 Service Unavailable`响应，以便客户端可以实现它的回退计划。但是，在队列中所有较早的请求都得到处理之前，我们不能返回响应。这意味着`503`响应不会立即发送，可能会在队列中等待很长时间。这反过来将使客户端等待响应，这可能会破坏其回退计划，尤其是如果该计划是在不同的实例上重试请求。

[![Diagram of an instance of React service which implements load shedding in conjunction with request queues. A request at the back of the queue, which is due to be shed, must wait for all of the other requests before actually being shed. Meanwhile, the developer also sheds... tears.](img/37411831166a261ebd6aba4e0b9955f3.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/shed-queue.svg)

如果我们希望减载有用，我们需要在收到注定要失败的请求后几乎立即发送`503`响应。

### 交错开口

经过一番头脑风暴后，我意识到我们可以通过交错请求呈现和分离来提供快速分离。

我构建了一个概念验证，将所有要呈现的请求推入一个呈现队列，用一个简单的数组实现。当一个新请求到达时，如果队列小于`m`——`m`是可以接受的最大并发请求数——我会将请求对象推入数组。如果队列变得太大，就会立即发送一个`503`响应。

当服务器启动时，我调用一个函数，从呈现队列的头部提取一个请求，并呈现它。当请求完成呈现后，发送响应，用`setImmediate()`递归调用函数。这将在节点事件循环处理累积的 I/O 事件之后安排下一个单个请求呈现*，给我们一个机会丢弃多余的请求。*

[![Diagram of how interleaved shedding works. 12 requests arrive as events in the Node event loop over time. If our max queue length is 3, the first 3 requests are placed into a rendering queue, which is implemented as an array. The next 3 requests are shed, with 503 responses sent for each. A setImmediate() callback, enqueued at application start, is then called, pulling request #1 from the render queue, rendering it, and sending a response to the client. The setImmediate() render callback recursively calls itself, enqueuing another render event behind requests 7 through 9, which arrived in the meantime. Request #7 is added to the render queue, bringing it back up to a maximum of 3 requests in queue. Requests #8 and #9 are shed, with 503 responses sent for each. The setImmediate() render callback is the next event in the Node event loop, and pulls request #2 from the render queue, rendering it, and sending a response. This cycle continues forever: render one request, then shed everything that doesn't fit in the render queue. This approach limits shed response latency to be approximately the processing time of the render request which lies ahead of it. This keeps our shed latency to reasonable, but not excellent, levels.](img/9676affa36bef520b2d80e76173b1544.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/interleaved-shedding.svg)

效果是呈现一个请求，然后*所有*多余的请求被丢弃，然后呈现另一个请求，依此类推。这种方法将 shed 响应延迟限制为大约是在它之前呈现的请求的长度。

当然，有可能提供更快的脱落。

### I/O 和工作进程

为了实现几乎瞬时的负载削减，我们重构了我们的应用程序，以产生一个节点进程的[集群](https://nodejs.org/api/cluster.html)。

想法很简单:将一个进程专门用于减载。当服务启动时，集群主进程派生出许多工作进程。主进程处理 I/O，接收传入的请求，如果工作进程太忙，立即返回一个`503`。如果工作线程空闲，主进程会向它发送请求。worker 执行所有繁重的工作，包括 React 组件渲染，并向 master 返回响应。主进程最后向客户端发送 HTTP 响应。

[![Diagram of how I/O and worker process architecture works, with 1 I/O process and 2 worker processes. Load Balancer sends 3 requests to the React service, which are received by its I/O process. The I/O process forwards requests #1 and #2 to each of the worker processes, but immediately sends a 503 response to shed request #3, since all of the workers are now considered busy. When the workers are finished processing requests #1 and #2, those responses are forwarded to the I/O process which sends them back to the Load Balancer for final delivery. This is a bit of a simplification: in reality, the I/O process can implement a queue so that you have a bit of elastic capacity in the event of fluctuating demand, or to implement a client-side rendering fallback threshold, etc.](img/63e11c5c6f21653333aed7722d10a58e.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/io-worker.svg)

这是我们交付生产的方法。尽管有点复杂，但它让我们可以灵活地试验各种数量的工作进程。同样重要的是，在向微服务架构发展的过程中，在我们可以获得延迟优势的地方取得优势。

## 组件缓存

### 缓存的想法

每当我们试图提高性能时，缓存的话题就会出现。开箱即用，React 服务器端呈现性能远不如 JSP 模板那样快，因此对 React 实现缓存策略有相当大的兴趣。

沃尔玛实验室开发了一个非常奇特的[缓存库](https://github.com/electrode-io/electrode-react-ssr-caching)，`electrode-react-ssr-caching`，它提供了基于每个组件的 HTML 输出缓存。对于动态渲染，可以缓存或插值属性值。这是一个令人印象深刻的系统。

哇，好快！自由使用缓存可以将渲染时间减少到亚毫秒级。这显然是提供最大性能增益的方法。

### 计算机科学中的两件难事

不幸的是，这种方法不是没有代价的。为了实现缓存，`electrode-react-ssr-caching`依赖 React 私有 API，并对其中一些 API 进行了变异。这有效地将库绑定到 React 15，因为 React 16 附带了 React 核心算法的完整重写。

更可怕的是，背景中隐约出现了那把旧锯子:

> 计算机科学只有两个硬东西:缓存失效和事物命名。菲尔·卡尔顿

事实证明，在每个组件的基础上实现缓存会产生很多微妙的问题。

### 缓存和插值

为了缓存渲染的 React 组件，`electrode-react-ssr-caching`需要知道如何处理组件的道具。有两种策略可用，“简单”和“模板”，但我将使用更具描述性的术语，“记忆化”和“插值”。

想象一个`<Greeting>`组件，它为用户呈现一个问候。为了简单起见，假设我们只支持英语和法语问候。组件接受一个`language`道具，可以是`en`或`fr`。最终，组件的两个版本将被缓存在内存中。

当使用记忆化策略时，组件被正常呈现，并且它的一个或多个属性被用于生成缓存键。每当相关的属性值改变时，不同的、渲染的组件副本被存储在缓存中。

[![Table illustrating that the 'Greeting_en' cache key corresponds with the '<p>Hello!</p>' rendered component HTML, and the 'Greeting_fr' cache key corresponds with the '<p>Bonjour!</p>' rendered component HTML.](img/6f99f55916cf9dba14a5faa0f62741d8.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/memoization.svg)

相比之下，插值策略将组件视为模板生成函数。它呈现组件一次，将输出存储在缓存中，对于后续的呈现，它将道具合并到缓存的输出中。

[!['Greeting' cache key corresponds with the '<p>@1@</p>' rendered component HTML template. When rendering a Greeting component with 'language' prop 'fr', the resulting HTML is '<p>fr</p>', which is obviously not what we want. When rendering a Greeting component with 'language' prop 'Bonjour!', the resulting HTML is '<p>Bonjour!</p>', which is the original intention.](img/b1d41d45d65e886c62bf5e8893413bcc.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/interpolation.svg)

需要注意的是，当我们使用插值时，我们不能简单地将语言代码传递给`<Greeting>`组件。*精确的*属性值被合并到缓存的组件模板中。为了呈现英语和法语消息，我们必须将这些确切的消息作为 props 传递给组件——条件逻辑在内插组件`render()`方法中是不可用的。

### 墨菲定律

我们如何为缓存的组件选择合适的记忆和插值策略？全局配置对象存储每个组件的策略选择。开发人员必须用缓存配置手动注册组件及其策略。这意味着，如果随着组件的发展，其适当的策略需要改变，开发人员必须记得更新缓存配置中的策略。墨菲定律告诉我们，有时我们会忘记这么做。这种对人类不犯错误的依赖的后果可能是惊人的。

假设我们的`<Greeting>`组件正在为它的 props 使用记忆化策略，而`language` prop 值仍然用于生成缓存键。我们决定显示一个更加个性化的问候，所以我们给组件添加了第二个道具，`name`。

[![Rendering a memoized Greeting component which receives a 'language' prop of 'en', and a 'name' prop of 'Brutus', will result in '<p>Hello, Brutus!</p>'.](img/0a4cce979addb6ec5147f0e60c7e560b.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/memoize-name.svg)

为了实现这一点，我们必须更新缓存配置中的组件条目，以便它使用插值策略。

但是如果我们忘记更新策略，*两个属性值*都将被记忆。在`<Greeting>`组件中呈现的前两个用户名将被缓存，每种语言一个，并且会意外地出现在所有用户面前！

[![Rendering a Greeting component which we intended to interpolate but accidentally memoized produces unexpected results. If the Greeting component receives a 'language' prop of 'en', and a 'name' prop of 'Brutus', and the cache key only takes the 'language' prop into account, it will result in '<p>Hello, Brutus!</p>'. If the Greeting component is rendered a second time with 'name' prop set to 'Not Brutus', the same HTML output is produced.](img/aa05ae9391c2fc98f3ba24be9fb9f682.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/memoize-gone-wrong.svg)

### 哦 FOUC！

情况变得更糟。因为组件缓存只用于服务器端渲染，而且因为我们所有的状态都存储在 Redux 中，所以当 React 挂载到浏览器中时，它的虚拟 DOM 将*不*匹配服务器端渲染的 DOM！React 将通过有利于虚拟 DOM 的协调来纠正这种情况。用户将会体验到类似于 T2 的无样式内容闪现(FOUC)T3。一瞬间会出现错误的名字，然后正确的会突然渲染！

[![Diagram of how a flash-of-unstyled-content (FOUC) and SEO problems can manifest when using per-component caching. React Service renders a memoized component with the value 'Brutus', although 'Not Brutus' is the dynamic value which should have been rendered. This mistake is sent to the Monolith, which dutifully includes it in the response to the browser. The browser initially displays 'Brutus', but React detects a discrepancy between the virtual and real DOMs, and re-renders the component, finally and correctly displaying 'Not Brutus', albeit after a FOUC. Meanwhile, Google receives the cached, server-side rendered, mistaken 'Brutus' response and indexes it forever and ever.](img/8f9a0f68255df417ead4c848da2574d9.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/fouc.svg)

现在假设这些内容被提供给一个搜索引擎爬虫。当人们查看页面时，他们不太可能注意到错误，因为客户端重新呈现会在眨眼之间修复问题。但是搜索引擎会索引不正确的内容。我们正处于严重的 SEO 缺陷的危险中，可能会持续很长一段时间，没有明显的症状。

### 爆炸缓存

更糟的是。假设我们的应用程序有 100 万用户，我们使用`language`和`name`属性值为`<Greeting>`组件生成缓存键。意外忘记从记忆化切换到插值意味着新的`name`道具，将使用一百万个唯一值进行渲染，将生成一百万个缓存条目。缓存的大小已经爆炸了！

[![Illustration huge explosion occurring on Earth due to passing millions of user names to an accidentally memoized component, which inflates memory usage until probably something explodes. Probably.](img/5983977ae92b2477119fca02ba566b5a.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/cache-explosion.svg)

如果该事故耗尽了可用内存，服务将终止。这种故障可能会悄悄逼近我们，因为缓存未命中不会同时发生。

即使我们设置了最大缓存大小，并采用了缓存替换策略，例如*最近最少使用的* (LRU)，缓存爆炸也会带来耗尽缓存存储的严重风险。本应被缓存的组件现在与所有其他碎片争夺缓存空间。缓存未命中将会增加，渲染性能可能会严重下降。

[![Comic of Star Trek bridge, with someone saying, 'Captain, our cache size has reached critical limits.' The Captain responds, 'Eject the warp core!' The caption reads, 'Don't let this happen to you.'](img/6f0ee7b16388c6a26138e5e9e4b253b7.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/warp-core.svg)

### 犯相反的错误

现在让我们想象一下，我们*确实*记得更新缓存配置，为我们的`<Greeting>`组件将适当的策略从内存化改为插值。如果我们这样做了，但是忘记更新组件的正确用法，我们将把一个损坏的组件交付给生产。

回想一下，内插属性值按原样合并到渲染组件模板中。组件的`render()`方法中的条件逻辑——比如根据`language`属性的值选择问候语——将只执行一次*。如果第一次渲染碰巧产生了一个英文问候语，那么模板将被缓存，并带有内置的英文问候语。对于所有后续的呈现，用户的名字将被成功地插入，但是问候语的其余部分将只以英语呈现。*

 *[![Diagram of interpolated Greeting component with 'language' and 'name' props being rendered for the first time, with values 'en' and 'Brutus', respectively. The 'language' prop value does not appear in the rendered output, but is instead used in a conditional to select either a 'Hello' or 'Bonjour' greeting. The resulting template is '<p>Hello, @2@!</p>'. The first interpolation of this template, using values 'en' and 'Brutus', produces the output '<p>Hello, Brutus!</p>'. The second interpolation of this template, using values 'fr' and 'Brutus', produces the output '<p>Hello, Brutus!</p>' again! This demonstrates how easy it is to introduce subtle bugs when using interpolation.](img/371acd4e6a19be6b42046b2fc5e6cabf.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/broken-interpolation.svg)

### 缓存规则着我周围的一切

无论我们从哪个角度来看，修改缓存组件的属性都充满了危险。开发人员必须特别注意确保为每个组件正确实现缓存。随着新特性的增加，React 组件会经历大量的变动，因此经常有机会进行无害的更改，这会破坏 SEO 性能，或者破坏呈现性能，或者呈现不正确的数据，或者为每个用户呈现私人用户数据，或者使 UI 完全瘫痪。

由于这些问题，我不建议将每个组件的缓存作为主要的扩展策略。速度的提高是令人难以置信的，当你已经没有其他选择的时候，你应该考虑实现这种类型的缓存。但是在我看来，同构渲染最大的好处就是统一了你的代码库。开发人员不再需要处理客户端和服务器端的逻辑，以及这种安排所带来的重复。潜在的微妙的、有害的错误需要非常仔细地考虑客户端和服务器端的渲染，这正是我们试图摆脱的浪费范例。

## 依赖关系

### 不要被黑

如果不提及我们通过保持依赖关系最新而获得的低得令人厌恶的性能优势，那将是我的失职。Node.js 和 React 等依赖项。

保持你的依赖关系最新是很重要的，这样你才不会被黑客攻击。如果你对此犹豫不决，就去问 Equifax [他们的结果如何](https://www.nytimes.com/2017/09/14/business/equifax-hack-what-we-know.html)。

[![A newscaster sitting at their newscaster's desk reads the following cast of news. 'Equifax revealed that a cyberattack potentially compromised confidential information of 143 million Americans. The breach was open from mid-May to July 29\. That was when Equifax first detected it. This security weakness was publicly identified in March and a patch to fix it had been available since then.](img/7a5cf9e09c8e02a62a216c2dfd884a46.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/equifax.svg)

### 你喜欢免费的东西吗？

但是这还不是全部！如果您现在行动，您的依赖性升级将免费带来*性能提升！*

因为我们在寻求提高性能，所以我们对主要依赖项的基准升级感兴趣。虽然您的里程可能会有所不同，但从节点 4 升级到节点 6 将我们的响应时间缩短了约 20%。从节点 6 升级到节点 8 带来了 30%的性能提升。最后，从 React 15 升级到 16 获得了 25%的性能提升。这些升级的累积效果是我们的性能提高了一倍多，因此服务能力也提高了一倍多。

[![Bar graph entitled 'Response Latency', showing relative improvements in response latency as dependencies are upgraded to newer major versions over time. Upgrading Node 4 + React 15 to Node 6 produced a 20% improvement in response times. Upgrading to Node 8 produced a further 30% improvement. Upgrading Node 8 + React 15 to React 16 produced another 25% improvement. That's a lot of free performance! Thanks, open source buddies!](img/79e52dfca916269dc968b018913b5993.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/dependencies.svg)

剖析您的代码也很重要。但是开源社区是一个巨大的人才海洋。非常聪明的人非常努力地工作，通常是免费的，以加快你的申请。他们站在一个繁忙的十字路口的拐角处，分发免费表演巧克力棒。拿一个，谢谢他们！

[![Comic of person handing out free performance chocolate bars to a turtle, saying, 'Free performance?' An arrow points out that the turtle is an 'Insecure turtle.' Slow, and without the latest dependencies, also insecure. Get it? GET IT? OH COME ON!!!1](img/22a5b43e53bdffbc72edeb9a5736df90.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/free-performance.svg)

## 同构渲染

### 浏览器作为你的服务器

同构渲染对于开发人员来说是一个巨大的简化助推器，他们长期以来被迫为客户端和服务器端的渲染上下文维护分离的模板和逻辑。通过将重新渲染卸载到 web 浏览器上，它还可以显著降低服务器资源消耗。用户浏览会话的第一页可以在服务器端呈现，在提供基本 SEO 的同时，提供了首次呈现的性能提升。所有后续的页面视图都可以从 JSON 端点获取数据，在浏览器中单独呈现，并通过 history API 管理浏览器历史。

[![Diagram illustrating how isomorphic rendering works. For the first page viewed, the browser sends a request to the Monolith, in this case for the /home page. The Monolith then requests that a Home component be rendered by the React service, which performs a server-side render and returns the result. The Monolith integrates this result into its response to the browser, and the user can now see the first page of their session. When the user navigates to their next page, the /search page, our React client-side app notices this, fetches data from the Monolith, and renders the page entirely on the client. Client-side renders are ideally used for every page in the session, except for the first one.](img/29c5f78aee42a4db5311b3312b1d4a4b.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/ssr-csr.svg)

如果一个典型的用户会话包含 5 个页面视图，那么只在服务器端呈现第一个页面将会减少 80%的服务器资源消耗。另一种思考方式是，它将实现服务器端渲染能力的 5 倍提升。这是一个巨大的胜利！

### 双页

在遗留应用程序中向这种能力发展需要耐心。前端的大爆炸式重写，除了极其危险之外，通常是不可能的，因为这是一个非常昂贵的前景。因此，需要一项长期的渐进战略。

我认为从页面对的角度来考虑这个问题是有意义的。想象一个简单的电子商务网站，有主页、搜索结果和单个产品页面。

[![Diagram of a common web page architecture, with many users starting on the Home page, proceeding to the Search page, which displays search results, and then finally ending up on a Product page.](img/a1b67e44d652d54ac04646e2569cda56.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/page-flow.svg)

如果您同时升级主页和搜索结果页面以利用同构呈现，大多数用户将首先访问主页，因此可以完全在浏览器中呈现搜索结果页面。搜索结果和产品页面组合也是如此。

[![Diagram of the Home, Search, Product page user flow, with the Home-Search and Search-Product page pairings highlighted. Ideally pages will transition to an isomorphic rendering strategy in adjacent pairs, such as those identified here.](img/fc01ffff30843224c2e6731e5c936772.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/strategic-page-pairings.svg)

但是很容易错过这些战略配对。假设你的搜索结果页面是赚钱的地方，所以产品团队不愿意修改它。如果我们把时间投入到改进主页和产品页面上，使它们在这个过程中同构，我们就不会看到客户端渲染有太多的提升。这是因为为了从主页进入产品页面，大多数用户会通过*到*导航搜索结果页面。因为搜索结果页面不是同构的，所以需要服务器端呈现。如果我们不小心，很容易执行一种逆[帕累托优化](https://en.wikipedia.org/wiki/Pareto_principle)，投入 80%的资源只获得 20%的收益。

[![Diagram of the Home, Search, Product page user flow, with the Home and Product pages having transitioned to an isomorphic rendering strategy, while the Search page remains server-side only. Since few users jump from the Home page to a Product page, client-side rendering cannot do much to reduce our server-side rendering load. Since many users progress from the Home page through Search to a Product page, these users are forced to experience a server-side render and full page refresh when transitioning into and out of the Search page.](img/239bf49678cea2e39bbd1f0fe481266f.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/inefficient-pairing.svg)

## 边际收益的聚合

令人惊讶的是，大量的小改进，当复合在一起时，可以产生一个巨大的性能提升。我最近了解到，术语*边际收益的聚集*描述了这种现象。众所周知，这与英国自行车运动的负责人戴夫·布雷斯福德有关，他[用这种哲学](https://hbr.org/2015/10/how-1-performance-improvements-led-to-olympic-gold)将英国自行车队变成了一支主导力量。

强调这些收益的*复合*效应是很重要的。如果我们实施两项改进，单独来看，可以使性能提高一倍，那么将它们结合起来*将使性能提高四倍*。各种固定成本和管理费用会影响最终结果，但一般来说这个原则适用。

人类心理学似乎与这种方法不一致。我们倾向于快速取胜和短期改善。我们倾向于不考虑总体改善的长期路线图，当然也不考虑它们的复合效应。这些倾向阻碍了我们探索可行的策略。将 React 服务器端渲染与传统的服务器端渲染模板进行比较，React 乍一看似乎“不可伸缩”。但是当我们对性能改进技术进行分层时，我们可以看到我们有巨大的性能提升空间。

我们能获得多少性能？我们应该以什么样的顺序追求这些技术呢？最终，具体的技术及其实现顺序将取决于您的具体情况。您的里程可能会有所不同。但是作为一个通用的起点来计划你的旅程，我推荐下面的方法。

1.  首先，升级您的节点并对依赖项做出反应。这可能是你最容易取得的绩效胜利。根据我的经验，从节点 4 和 React 15 升级到节点 8 和 React 16，性能提高了大约 2.3 倍。
2.  仔细检查您的负载平衡策略，并在必要时进行修正。这可能是下一个最容易的胜利。虽然它不能改善平均渲染时间，但我们必须始终为最坏的情况做准备，因此在我看来，减少 99%的响应延迟也算是一种容量增加。我保守地估计，从随机负载平衡切换到循环负载平衡为我们带来了 1.4 倍的增长空间。
3.  实现客户端呈现回退策略。如果您已经在服务器端呈现一个序列化的 Redux 存储，这是相当容易的。根据我的经验，这在应急弹性能力方面提供了大约 8 倍的改进。这种功能可以为您推迟其他性能升级提供很大的灵活性。即使你的表现很好，有一个安全网总是好的。
4.  结合客户端路由，实现整个页面的同构呈现。这里的目标是服务器端只呈现用户浏览会话中的第一页。使用这种方法升级遗留应用程序可能需要一段时间，但是可以逐步完成，并且可以通过升级战略页面对来进行帕累托优化。所有的应用程序都是不同的，但是如果我们假设每个用户会话平均访问 5 个页面，那么使用这种策略我们可以将容量提高 5 倍。
5.  在低风险区域安装每个组件的缓存。我已经概述了这种缓存策略的缺陷，但是某些很少修改的组件，比如页眉、导航和页脚，提供了更好的风险回报比。当缓存一些很少修改的组件时，我看到容量增加了大约 1.4 倍。
6.  最后，对于需要最大风险和最大回报的情况，缓存尽可能多的组件。使用这种方法可以轻松实现 10 倍或更大的容量提升。然而，它确实需要非常仔细地关注细节。

[![Bar graph of relative capacity increase provided by compounding each successive technique. Baseline is 1x. Upgrading Dependencies produces another 2.3x improvement for 2.3x total capacity. Fixing Load Balancing produces another 1.4x improvement for 3.2x total capacity. Client-Side Fallback produces another 8x improvement for 25x total capacity. Isomorphic rendering produces another 5x improvement for 128x total capacity. Some Caching produces another 1.4x improvement for 180x total capacity. Maximum Caching produces a 10x improvement on top of all other techniques, for 1288x total capacity. All bars are rendered as precariously stacked React service instances. Maximum Caching bar contains a stick figure holding up some of the instances, in a nod to the ongoing maintenance that technique requires.](img/fa9b6f6bd6946619b4d1bf2b045ae616.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/1288x.svg)

根据合理的估计，当我们综合这些改进时，我们可以在总容量方面实现惊人的 1288 倍的改进！您的里程数当然会有所不同，但是三个数量级的改进可以很容易地改变您的技术策略。

## 你所有的服务器都属于 Redux

我对 React 服务器端渲染的可行性感觉好多了，现在我已经经历了火灾，只受了轻微的烧伤。就像技术世界中的几乎所有事情一样，第一次探索一种方法会承担大部分成本。但是，即使你让别人去开拓创新，你还是会有第一次。你无法逃避。等待别人完善仰泳是一种非常缓慢的学习游泳的方式。

[![Comic of a beach, with a body of water in the background. Lots of people are swimming and playing in the water, while a single person stands on the beach and addresses the reader: 'I'm not sure that swimming is production-ready yet.' The sun shines in the sky, wearing sunglasses, as suns do.](img/633c55b2224da5dc12bdbd17da5f0a3e.png)T2】](//arkwright.github.img/scaling-react-server-side-rendering/swimming.svg)

我对这个话题的了解比我刚开始的时候多了很多。这并不是说我的下一次尝试将没有问题，但是知道许多活板门和电源在哪里可以很容易地使下一个项目便宜一个数量级。我期待着这样一个世界，在那里，面向组件的同构架构是标准的方法，而不是向往的东西。我们快到了。

附:非常感谢您花时间读到这里！这对我意义重大！我刚好在寻找新的机会，所以如果你喜欢这篇文章，并且想和我一起工作，请不要犹豫[联系](https://twitter.com/arkwrite)。祝你过得愉快！*