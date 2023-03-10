# 什么是无服务器？

> 原文：<https://dev.to/aditichaudhry92/what-is-serverless-1bf>

这篇文章最初发表在 Medium 上。你可以在这里看一看它[。](https://medium.com/@aditi.chaudhry92/what-is-serverless-6052d5301a7f)

以前有人告诉我，无服务器很棒，因为代码不在服务器上运行。显然代码不是在稀薄的空气中运行的，那么它在无服务器架构中的什么地方运行呢？什么是无服务器，为什么有这么多关于它的宣传？炒作到底值不值？

术语“无服务器”容易引起误解，因为服务器仍然存在，但开发人员不需要担心管理服务器。走向无服务器意味着开发人员在任务层而不是服务器层关注应用程序。现在，他们不需要担心在云中或本地管理和操作服务器或运行时。

无服务器也可以被描述为功能即服务(FaaS)。FaaS 产品执行响应事件而按需运行的功能(代码)。按需运行代码的好处是在您的代码不需要全天运行的用例中。当您使用无服务器产品时，您只需为您的应用程序消耗的资源付费，而不是预先购买的单元。在某些情况下，这可以节省你的钱。将代码分解成函数的另一个好处是，扩展和部署单个函数要容易得多。让我们想象一个特定的函数在计算上比另一个函数更重；在无服务器架构中，这些功能可以单独扩展，而不必扩展整个应用程序。

这听起来有点像微服务，概念也差不多。微服务的目标是将您的大型应用程序分解成小的、解耦的、独立的系统，这些系统重新连接在一起，使您的应用程序正常工作。功能更进一步，比微服务更小，认为一个微服务可以包含多个功能。不同之处在于用例。有些东西不适合函数，反之亦然。最终，功能/无服务器和微服务都在开发中占有一席之地，但各有优缺点。

那么无服务器的优势或好处是什么呢？我们讨论了可伸缩性和成本，但还有其他问题吗？作为开发人员，无服务器很好，因为您不需要提供或维护任何服务器。如果您只想编写响应事件的代码，但不需要创建一个完整的应用程序，那么无服务器非常棒。此外，一些无服务器服务，如 AWS Lambda，默认情况下提供可用性和容错，因此作为开发人员，您不用担心两件事。此外，无服务器可以提高应用程序的弹性。因为您的代码不是托管在一个特定的服务器上，所以不会有单点故障。如果运行代码的机器出现故障，无服务器提供者会将您的代码切换到新的机器上，不会对用户体验产生任何影响。基本上，如果开发人员不担心服务器，他们可以把时间和精力花在开发可伸缩和可靠的产品上。

但是有人仍然需要担心服务器，因为我们的代码不会在《永不永不着陆》中神奇地运行。即使从运营的角度来看，也有节省资金的潜力。首先，您不必 24/7 全天候运行您的应用程序并为其付费，只需按需即可。此外，就像使用微服务一样，使用无服务器基础架构，您可以优化您的资源，这也可以降低成本。

到目前为止，无服务器似乎很棒，但是在大肆宣传的背后也有一些不利因素。首先是设计和维护无服务器架构的复杂性。将您的应用分成微服务会带来复杂性。将您的微服务进一步细分为多个功能会将复杂性提升到一个全新的水平。设计和构建分布式计算架构存在架构上的复杂性。维护这样的系统很复杂，调试分布式系统也很痛苦。到目前为止，还没有多少工具可以帮助开发人员监控/调试无服务器环境，但我相信很快就会有了。

第二个缺点是性能。在无服务器架构中，当您的功能响应应用程序的触发/需求时，您将会经历更长的延迟。过了一段时间后，当你的功能第一次被触发时，一台机器必须加速运转才能执行你的功能。在 AWS Lambda 中，在函数第一次执行后，您的实例会保持运行大约 10 分钟，因此它可用于任何后续调用。10 分钟后，您将不得不再次等待一个新实例启动。如果性能是重中之重，我会坚持使用分配的服务器(云服务器或本地服务器)。

第三个问题是供应商锁定。从一个供应商转换到另一个供应商可能需要您更新/更改您的代码、操作工具、设计和架构。因此，将代码从一个解决方案转移到另一个解决方案需要大量的精力。目前市场上有几家供应商。以下是前 4 名的简要概述(排名不分先后):

1.  **AWS Lambda** 可能是最大也是最知名的无服务器框架。它从 Node.js 开始，但支持 Java 和 Python。AWS Lambda 使用方便，因为它与其他 AWS 服务和 Alexa 技能工具包集成在一起。开发人员可以使用交互式控制台和/或命令行工具来上传和管理代码。
2.  **谷歌云功能**是 AWS Lambda 的竞争对手，运行在谷歌的公共云基础设施上。这个平台只支持 Node.js，谷歌云功能和 AWS Lambda 的一个主要区别在于，AWS 提供了更多可以和 Lambda 集成的服务。只有谷歌云存储等少数服务集成了谷歌云功能。
3.  **IBM OpenWhisk** 是 AWS Lambda 的开源替代产品，并与 IBM Bluemix 集成。OpenWhisk 支持 Node.js 和 Swift。开发人员可以通过 CLI 与框架进行交互，并可以在本地 Ubuntu 机器上安装 OpenWhisk。我认为 OpenWhisk 最好的部分是它可以与任何支持 Webhooks 的第三方服务集成。
4.  **微软 Azure 功能**支持包括 JavaScript 和 Python 在内的多种语言。微软在其门户中提供了一个 IDE 来帮助开发人员原型化和部署他们的功能。

我相信 AWS Lambda 是最成熟的无服务器框架。AWS 在 2014 年推出了 Lambda，谷歌和微软在两年后推出了他们的替代解决方案。AWS Lambda 支持请求链和边缘处理等高级功能，并集成到多种 AWS 产品中。

总之，无服务器值得大肆宣传吗？在某些使用案例和情况下，无服务器可能会非常出色。如果您的应用程序是事件驱动的或偶尔使用的，并且性能不是问题，那么这是消耗云资源同时保持低成本的好方法。然而，对于其他情况，例如当您有一个长时间运行的任务/服务时，它并不理想。在这些情况下，无服务器可能会让您花费更多的钱，从而抵消其主要优势之一。我认为现在无服务器还有点年轻，企业仍在试图确定它是否是适合他们的解决方案。然而，我对无服务器计算的未来感到非常兴奋，因为我相信它有巨大的潜力，尤其是随着更多监控和调试功能的工具的发展。

这是我的“什么是”科技博客系列的第八篇文章。我会每周在这里和我的博客上写更多！