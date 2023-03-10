# 如何监控 SRE 黄金信号

> 原文：<https://dev.to/stevemushero/how-to-monitor-the-sre-golden-signals-9oc>

[![Binoculars](img/74b1b971d2ff42db10743b5c1d47108b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--If4Lv-EQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gq4f1gsvmte9zv61s79o.jpg)

站点可靠性工程(SRE)和相关概念最近非常流行，部分是由于著名的[谷歌 SRE 的书](https://smile.amazon.com/Site-Reliability-Engineering-Production-Systems/dp/149192912X/ref=sr_1_1)和其他人谈论“黄金信号”,你应该监视这些信号以保持你的系统在扩展时快速可靠。

每个人似乎都同意这些信号很重要，但是你实际上如何监控它们呢？似乎没人多谈这个。

这些信号比传统的 CPU 或 RAM 监控更难获得，因为每个服务和资源都有不同的指标、定义，尤其是所需的工具。

微服务、容器和无服务器使获取信号变得更具挑战性，但仍然是值得的，因为我们需要这些信号来避免传统的警报噪声，并有效地对我们日益复杂的分布式系统进行故障排除。

本系列文章将介绍一些常见服务的信号和实用方法。首先，我们将简要讨论信号本身，然后简单介绍如何在您的监控系统中使用它们。

最后，还有一个关于如何监控信号的特定服务指南列表，适用于负载平衡器、Web & App 服务器、DB & Cache 服务器、通用 Linux 等。随着我们不断寻求反馈和更好的方法来获得更好的数据，这个列表和细节可能会随着时间的推移而变化。

## 一、SRE 信号有哪些？

有三种常见的列表或方法:

*   来自[谷歌 SRE 的书](https://smile.amazon.com/Site-Reliability-Engineering-Production-Systems/dp/149192912X/ref=sr_1_1):延迟、流量、错误和饱和度
*   [使用方法](http://www.brendangregg.com/usemethod.html)(来自 Brendan Gregg):利用率、饱和度和错误
*   红色方法(来自汤姆·威尔基):比率、错误和持续时间

你可以看到重叠部分，正如 Baron Schwartz 在他的 Monitoring &[Observability with USE 和 RED blog](https://www.vividcortex.com/blog/monitoring-and-observability-with-use-and-red) 中指出的，每种方法的侧重点不同。他认为使用是关于内部视图的资源，而红色是关于请求、实际工作，因此是外部视图(从服务消费者的角度)。它们显然是相关的，也是互补的，因为每个服务都消耗资源来工作。

出于我们的目的，我们将关注五个信号的简单超集:

*   **速率**--请求速率，以请求/秒为单位
*   **错误**--错误率，以错误/秒为单位
*   **延迟**响应时间，包括排队/等待时间，单位为毫秒。
*   **饱和度**——超载程度，这与利用率有关，但更直接地通过队列深度(有时是并发性)来衡量。作为一个队列度量，这在你饱和的时候就变成非零了，以前往往不多。通常是柜台。
*   **利用率**——资源或系统的繁忙程度。通常表示为 0-100%，对预测最有用(因为饱和度可能更有用)。请注意，我们并没有使用利用率法则来得到这个值(~率 x 服务时间/工人)，而是寻找更熟悉的直接测量方法。

其中，饱和度和利用率通常是最难获得的，并且充满了假设、警告和计算复杂性——充其量将它们视为近似值。然而，它们通常对寻找当前和未来的问题最有价值，所以我们忍受着去爱它们。

所有这些度量都可以通过各种方式进行拆分和/或聚合。例如，HTTP 可以分解为 4xx 和 5xx 个错误，就像 URL 可以分解延迟或速率一样。

此外，还有更复杂的计算方法。例如，错误通常比成功的请求有更低的延迟，所以如果可能的话，您可以从延迟中排除错误(通常您不能)。

尽管这些拆分或聚合非常有用，但它们已经超出了本文的范围，因为它们更接近于度量、事件、高基数分析等。让我们先集中精力获取基本数据，因为这已经够难的了。

### 现在我们有了信号，我们该怎么处理它们呢？

如果你愿意，你可以直接跳到这篇文章末尾的实际数据收集指南，但是我们应该讨论一旦有了这些信号该怎么做。

这些是“黄金”信号的一个重要原因是，它们试图测量直接影响最终用户和系统生产部分的事物——它们是对重要事物的直接测量。

这意味着，从理论上讲，它们比许多不太直接的度量方法更好、更有用，例如 CPU、RAM、网络、复制延迟和无数其他东西(我们应该知道，因为我们经常在每台服务器上监视 200 多个项目；从来都不是好时机)。

我们收集黄金信号有几个原因:

*   警报——当出现问题时告诉我们
*   故障排除-帮助我们找到并解决问题
*   调整和容量规划——帮助我们不断改进

我们这里的重点是警告，以及如何警告这些信号。之后你做什么是你和你的信号之间的事。

警报传统上使用静态阈值，在我们心爱的(哈！)Nagios，Zabbix，DataDog 等。系统。这是可行的，但是很难设置好，并且会产生很多警告噪音，因为你(和你一起生活的任何人)很可能会强烈地意识到这一点。

但是根据您的经验和最佳实践，如果必须的话，可以从静态开始。当设置为我们非常确定有问题，或者至少有异常情况发生的级别时(例如，95%的 CPU、超过 10 秒的延迟、中等大小的队列、超过每秒几个的错误率等)，这些通常工作得最好。)

如果您使用静态警报，不要忘记下限警报，例如接近零的每秒请求数或延迟，因为这些通常意味着有问题，即使在凌晨 3 点流量较低时也是如此。

#### 你是平均值还是百分位数？

这些警报通常使用平均值，但请尽量使用中值，因为这些值对大/小异常值不太敏感。正如 Optimizely 在他们的博客中指出的，平均值也有其他问题。尽管如此，只要你的测量窗口很短(例如 1-5 分钟)，平均值/中间值很容易理解，容易获得，并且作为一个信号非常有用。

更好的是开始考虑百分位数。例如，您可以对 95%的延迟发出警报，这是对您的用户来说事情有多糟糕的一个更好的度量。

然而，百分位数比它们看起来更复杂，当然，Vivid Cortex 有一个关于这一点的博客:为什么百分位数不以你认为的方式工作，例如，他警告说，你的系统实际上是在你的测量时间内(例如 1 或 5 分钟)平均百分位数。但是它对于提醒仍然是有用的，如果可以的话，你应该尝试一下(你经常会震惊于你的百分位数有多差)。

#### 你是一个异数，还是只是怪异？

理想情况下，你也可以开始在你闪亮的新黄金信号上使用现代异常检测。异常检测对于捕捉发生在非高峰时间或导致异常低的指标值的问题特别有用。此外，它们允许更严格的警报范围，因此您可以更早地发现问题(但不要太早，以免淹没在错误的警报中)。

然而，异常检测可能相当具有挑战性，因为很少有内部监控解决方案能够做到这一点(Zabbix 做不到)。它也是相当新的，仍在发展，很难调整好(特别是在我们的黄金信号中常见的“季节性”和趋势)。

幸运的是，较新的 SaaS /云监控解决方案，如 DataDog、SignalFX 等。可以做到这一点，像 Prometheus & InfluxDB 这样的新本地系统也可以做到这一点。

不管您的异常工具是什么，Baron Schwartz 有一本关于这方面的好书，您应该读一下，以便更好地理解各种选项、算法和挑战:用于监控的异常检测。

#### 我能见你吗？

除了提醒，你还应该想象这些信号。Weave Works 的格式很好，有两个图形栏，Splunk 的视图也很好。左边是请求和错误率的堆叠图，右边是延迟图。您也可以添加第三个混合饱和度/利用率图表。

您还可以使用标签/事件来丰富您的指标，例如部署、自动扩展事件、重启等。理想情况下，像 Netsil 一样在系统架构图上显示所有这些指标。

#### 修理我，修理你

关于警报的最后一点，我们发现 SRE 黄金信号警报更难响应，因为它们实际上是潜在问题的症状，很少被警报直接暴露。

这通常意味着工程师必须有更多的系统知识，更善于挖掘问题，这很容易存在于十几个服务或资源中

工程师们总是不得不将所有的点连接起来，并在警报之下(或之上)挖掘，即使是基本的高 CPU 或低 RAM 问题。但是黄金信号通常更加抽象，并且很容易有很多黄金信号，例如，低级服务上的单个高延迟问题很容易在整个系统中引起许多延迟和错误警报。

黄金信号有助于解决的一个问题是，我们往往只有少数服务的有用数据，前端问题会导致长时间寻找罪魁祸首。

收集每个服务的信号有助于确定哪个服务是最可能的原因(特别是如果您有依赖信息的话)，从而确定关注的重点。
就这样。享受你的信号，因为发现、监控和提醒它们既有挑战性又很有趣。

### 从每个服务获取数据

下面是各种流行服务的附录文章，我们正在努力随着时间的推移添加更多。同样，我们欢迎对此的反馈。

请注意，以一种可用的方式获取这些数据有许多细微差别和挑战，因此，在我们平衡清晰和有点彻底之间的关系时，提前为所有的注释和警告道歉。

还要注意，在某些情况下，您必须自己进行处理，比如在对基于计数器的指标进行采样时进行增量计算(大多数系统会自动为您完成这项工作)。

名单上:

*   [**负载平衡器**](https://medium.com/p/de8a98c74020) 奥斯·阿尔伯/ELB，哈普罗西
*   [**网络服务器**](https://medium.com/@steve.mushero/web-servers-sre-golden-signals-9463c96a3db3)——Apache&Nginx
*   [**应用服务器**](https://medium.com/@steve.mushero/app-servers-sre-golden-signals-e13e9330022f)——PHP、FPM、Java、Ruby、Node、Go、Python
*   [**数据库服务器**](https://medium.com/@steve.mushero/mysqls-sre-golden-signals-67e2adf88824)MySQL&AWS RDS
*   [**Linux 服务器**](https://medium.com/@steve.mushero/linuxs-sre-golden-signals-af5aaa26ebae)——作为底层资源

由于这是一组冗长而复杂的文章，无疑会有不同的观点和经验，因此我们欢迎反馈和其他想法。我们将根据反馈和其他人的经验修改文本，因此请不时查看您喜欢的服务的更新。

这篇文章最初出现在媒体上。