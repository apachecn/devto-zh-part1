# 星期五爆炸#14

> 原文：<https://dev.to/horia141/friday-blast-14-2he9>

[按概率对 if…else 语句进行排序有什么影响(2017)](https://stackoverflow.com/questions/46833310/what-is-the-effect-of-ordering-if-else-if-statements-by-probability)——从理论和经验上来说，按被采用的概率递减的顺序对分支进行排序会产生更好的性能。当然，你只可能在微基准测试中看到它。但这仍然很有趣，因为我使用的“可读性”最佳实践是首先在函数和循环中使用可能性最小的情况。因为它们很可能是错误处理程序，而不是真正的代码，所以将大部分方法留到最后。

[开发人员如何最好地协商他们的薪水(2017)](https://stackoverflow.blog/2017/10/16/developers-can-best-negotiate-salary/)——我的同事[尼克·拉森](https://twitter.com/fody?lang=en)写的一篇关于薪水协商的一些“技术”方面的文章。也就是相对于市场给自己定价。除了非常好的散文，我还喜欢它没有依赖心理学或“谈判理论”工具的事实。虽然有用，但对于一年谈判一两次的人来说，可能很难掌握。

[为什么 Trello 未能建立十亿美元的业务(2017 年)](https://blog.usejournal.com/why-trello-failed-to-build-a-1-billion-business-e1579511d5dc) -主要是因为他们没有*尝试*或者没有成功成为人们工作流程的中心。这意味着各种垂直行业的专业化以及与所有其他中小企业&企业工具(GitHub、Slack、吉拉等)的集成。).过了一段时间，许多其他工具复制了他们漂亮的用户界面，但最终还是被 Atlassian 收购了。

[最先进的 JavaScript 2016 (2016)](https://medium.com/javascript-and-opinions/state-of-the-art-javascript-in-2016-ab67fc68eb0b) - SPAs，React，Redux，Webpack，ES6 或者 TypeScript 如果必须的话，，Mocha+Chai+Sinon，Lodash，fetch。很多东西，但每一个本身都很小。这份名单在 2017 年基本保持不变，Vue.js 是 React 的竞争者。

[采用持久存储的应用架构(2014)](http://firstclassthoughts.co.uk/Articles/Design/ApplicationArchitecturesWithPersistentStorage.html) -这是一部关于人们如何围绕数据库组织应用的精彩历史和概述。涵盖了从“所有应用程序使用一个中央数据库”到“每个应用程序有几个专门用于不同任务的数据存储库”(即微服务)。注意，在如何组织服务及其依赖的数据库的“物理层”和如何组织类似 SaaS 的客户的“逻辑层”之间存在不匹配。

为什么脸书、Twitter 和 Digg 如此难以扩展？(2009)——你可以把 Digg 和脸书、Twitter 放在一个类里，就知道这是一篇旧文章。原因是一个用户的数据集实时依赖于其他用户的数据集。所以水平缩放的常用技巧不起作用。脸书采取的方法是对每个朋友进行投票，以建立一个回应，从而将负载转移到“阅读时间”，而 Twitter 采取的方法是将推文发送到追随者的收件箱，从而将负载转移到“写作时间”。

[发布工程介绍(2016)](https://www.oreilly.com/ideas/introduction-to-release-engineering) -非常大的公司，如谷歌、微软、脸书等。让一大群开发人员致力于“基础设施”的特定方面。一些人致力于测试工具，一些人致力于构建工具，还有一些人致力于“发布工具”。这篇文章是关于后者，以及如何做到这一点。对于大多数开发团队来说，这是一个很好的建议。尽管大多数人没有足够的带宽来构建他们自己的系统，并且不得不按照流程和惯例来完成谷歌的工具所执行的一些事情。

[谷歌十年的容器控制(2016)](https://www.nextplatform.com/2016/03/22/decade-container-control-google/) -谷歌内部容器的一点历史以及从他们早期的集群管理软件到 Kubernetes 的演变。