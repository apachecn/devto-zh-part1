# GSoC——社区结合期

> 原文：<https://dev.to/venkat2811/gsoc-community-bonding-period-1mcn>

[![](img/d0f7d8cb241857c461f2b86668ade21f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F3nidROn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1668355858026/6k2Bc01MG.png)

今年，社区联络期从 4 月 22 日到 5 月 22 日..尽管我甚至在 GSoC 项目结果公布之前就和我的导师和社区成员频繁交流(耶！！，我相当自信)，这段时间非常非常有帮助，非常有用。

我从 4 月 29 日到 5 月 10 日请假去参加学期考试，我的导师们对此很满意。在此之前，我们进行了一次聚会，我的导师向我们解释了在这个社区交流阶段的期望。从我写建议书到现在，代码库发生了重大变化。这里是[以前的代码库](https://github.com/wso2/product-gw/)，这里是[的新代码库](https://github.com/wso2/carbon-gateway-framework)。右大变。？是的，我也有同样的感觉，我慌了！！但是我的导师和 org 的成员们非常乐于助人，理解我的处境。他们很好地指导了我。

**为什么选择负载平衡器？**

我的项目是在 WSO2 网关上构建一个 HTTP 负载平衡器。当我把这件事告诉我的朋友时，很少有人感到惊讶(是的，我想做一个很棒的产品！)很少有人问我为什么要使用负载平衡器。？既然已经有了，为什么还要重新发明轮子。？。首先，我选择这个组织是因为， [WSO2 ESB](http://wso2.com/products/enterprise-service-bus/) 是 ebay 的中坚力量！是的，它帮助 ebay 每天处理[超过 10 亿笔交易](http://wso2.com/casestudies/ebay-uses-100-open-source-wso2-esb-to-process-more-than-1-billion-transactions-per-day/)！！很酷吧..？所以这和 gateway 有什么关系。？这个网关框架将用于构建下一代 ESB。

WSO2 网关是一个基于标准网关模式的高性能、轻量级、低延迟的消息传递网关。其基于 Netty 的非阻塞 IO 和中断器(ringbuffer)架构使其成为最快的开源网关。[性能指标评测](http://www.slideshare.net/kasun04/wso2-gateway?qid=9c8d89a4-e982-4883-87a8-ac2dca7bf223&v=&b=&from_search=1)(幻灯片 13、14、15)显示，与其他解决方案相比，网关数量非常高，接近直接基于 netty 的后端(没有任何中间网关)。

***特性:***

*   支持不同的负载平衡算法
*   完全符合 HTTP 规范
*   支持 SSL
*   会话持久性
*   运行状况检查和重定向

因此，我正在构建一个非常酷的 LB，它基于非阻塞 IO 来实现高性能和低延迟的中介。如果一切顺利，我也希望如此，总有一天，这个 LB 会被很多组织使用**。**

**迄今为止的成就:**

*   了解[carbon-gateway-framework](https://github.com/wso2/carbon-gateway-framework)代码库，试用几个[产品集成服务器](https://github.com/wso2/product-integration-server) **的样本。**
*   用于读取负载平衡器特定配置的语法支持。
*   [独立 HTTP 负载平衡器服务器](https://github.com/Venkat2811/product-http-load-balancer) **的存储库结构。**

你们可以在上面提到的链接中找到我的代码。感谢阅读。

*原载于 2016 年 5 月 23 日 https://venkat.eu/gsoc-community-bonding-period-b0d8f36d918*[。](https://venkat.eu/gsoc-community-bonding-period-b0d8f36d918)**