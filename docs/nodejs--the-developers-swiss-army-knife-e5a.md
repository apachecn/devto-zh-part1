# node . js——开发者的瑞士军刀

> 原文：<https://dev.to/voucherify/nodejs--the-developers-swiss-army-knife-e5a>

在阅读了 Anthony 的关于 JavaScript 如何吞噬世界的文章后，我想和 T2 分享一下我们在这项技术上的经验。虽然 Anthony 关注的是大型公司，但我想展示不同行业中使用 Node 和 JS 的**小型**(但发展迅速的ðÿš€)公司的例子。这是最近出现在我们博客上的一篇文章(你可以跳过介绍直接跳到案例分析)。

-

在创建 8 年后的今天，Node.js 的人气正在绽放。它已经通过了 Java 社区的评论，培养了许多大公司的早期采用者，甚至进入了太空。在这篇文章中，我想分享关于这一现象的两件事:首先，我们现在看到的 Node.js 的增长有多可观，其次，新的和值得关注的公司如何使用它来支持他们的业务。

[![](img/a5904cc1619d5ddc06e3a068e6c6a7f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--spqXuRlp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hrscywv4p/image/upload/c_limit%2Cfl_lossy%2Ch_9000%2Cw_1200%2Cf_auto%2Cq_auto/v1/86312/node_js_examples_nasa_using_it_to_desing_spacewalks-1465910222073_whanuw.png)

## Node.js 和 JavaScript

Node 的名气一部分来自于 JavaScript 的流行。最近的 Stack Overflow(程序员问答门户)[调查](http://stackoverflow.com/insights/survey/2017/)显示，根据 64，000 名程序员的说法，JavaScript 是最受欢迎的编程语言。JavaScript 之所以成为主流，主要是因为它是一项网络原生技术，而网络正在吞噬这个世界。但是 JavaScript 也已经成为其他地方的领先技术。令人惊讶的是，JavaScript 不仅领先于 web 开发，它还征服了桌面、Devops，甚至数据工程。这些图表不言自明:

[![](img/6bde1120f66d9135bdc5fcac2f5626e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xtBSeIpH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hrscywv4p/image/upload/c_limit%2Cfl_lossy%2Ch_9000%2Cw_1200%2Cf_auto%2Cq_auto/v1/86312/node_1_xzx2xd.png)
[![](img/50e12d80fdc75d38090833e9b4083630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aSnsfIIy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hrscywv4p/image/upload/c_limit%2Cfl_lossy%2Ch_9000%2Cw_1200%2Cf_auto%2Cq_auto/v1/86312/node_2_gzpyvc.png)
[![](img/cd4620a18b6a437f2ed1f7a35b677e8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oCeq6pd8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hrscywv4p/image/upload/c_limit%2Cfl_lossy%2Ch_9000%2Cw_1200%2Cf_auto%2Cq_auto/v1/86312/node_3_jifqk4.png)
[![](img/620c8bcc0b4ee707cf1f37c5e6e1ed4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wtyxol3b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hrscywv4p/image/upload/c_limit%2Cfl_lossy%2Ch_9000%2Cw_1200%2Cf_auto%2Cq_auto/v1/86312/node_4_yznfgu.png)

无论如何，结论是，了解 JavaScript，显然有很大的机会在软件开发的不同领域使用它。此外，我认为在一个组织的不同项目中使用相同的编程语言也是提高开发人员生产力的一种方式。

让我们回到数据上来:堆栈溢出调查证实了 GitHub 统计已经显示的内容；谈到在 GitHub 平台上积极开发的项目数量，JavaScript 胜过 Java、Python 或 PHP。值得注意的是，这不是昨天的时尚，这是一种趋势，已经持续出现了几年。

最后，软件咨询公司 RedMonk 从 2010 年开始分析软件趋势。上周，他们制作了一个信息图表，展示了 StackOverflow 和 GitHub 数据之间的关系。如果 JavaScript 在这两方面都是明显的赢家，那么这并不奇怪:

[![](img/52651336fe1c25ff8ef9a127138bffa8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lt7JkyJH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hrscywv4p/image/upload/c_limit%2Cfl_lossy%2Ch_9000%2Cw_1200%2Cf_auto%2Cq_auto/v1/86312/node_5_llzpec.png)

然而，我们不得不承认，JavaScript 峰值的一部分可以反过来归因于 Node 的增长。我猜这些生长物以某种方式交织在一起，但没有人能真正说出精确的相关性。

## Node.js 和工装

JavaScript 的流行带来了许多有用的工具和框架。他们的目标是提高开发人员的生产力，并最终缩短上市时间。在 2016 年，Node.js 开发研讨会配备了一些新的有趣的工具:

*   FogCreek 是纽约一家历史悠久的软件公司，以建立 Trello 而闻名，它推出了 [Glitch](https://glitch.com) 。这个小而强大的工具允许开发人员快速开发 Node.js 原型。Glitch 最酷的一点是，您可以只使用浏览器来编码、运行和托管一个成熟的 web 应用程序！最重要的是，两个或更多的开发人员可以在项目上实时协作(就像在 Google Docs 中一样)。即使新开发人员远程工作，这个特性对他们也很有用。最后，当您的 MVP 应用程序或微服务框架准备好结晶时，您可以将其导出到 GitHub，并在标准的日常环境中继续工作。
*   Zeit 的旗舰产品“ [Now](https://zeit.co/now) ”，想把云部署流程短路。现在允许您在云中托管 JavaScript 网站、应用程序和服务，而无需复杂的云提供商设置。他们负责源代码控制和应用程序的可伸缩性。要让您的应用程序运行，您所要做的就是运行一行代码。多酷啊。
*   最后，脸书发布了一个名为 [Yarn](https://techcrunch.com/2016/10/11/facebook-partners-with-google-others-to-launch-a-new-javascript-package-manager/) 的新包管理器。它的主要任务是使访问 JavaScript 库更快更安全。全世界的开发者都接受了它。甚至 Rails(最流行的非 javascript web 技术之一)也将 Yarn 整合到了他们的框架中。

正如您所看到的，大量的互联网玩家、软件传奇和新生都已经接受了 JavaScript 和 Node.js 世界，并且他们通过发布开源开发工具继续致力于此。但这只是冰山一角。几乎每天，社区都会发布许多有用的工具。根据我们上面看到的研究，JavaScript 潮流不会很快放缓。

更好的工具转化为越来越多的合格开发人员。他们喜欢让他们更有生产力的新技术，正是 JavaScript(尤其是 Node.js)开发人员的增加吸引了早期的大多数公司使用这项技术。

现在，让我介绍 5 家在业务中成功使用 Node.js 的快速增长的互联网公司。

## 社交中的 node . js-Jodel

Jodel 应用程序允许社区实时显示他们所在地区发生的事情。他们开始是一个以校园为中心的应用程序，但地理定位推文很快变得流行起来，并出现在大学里。现在，已经有超过 500 万人下载了这个应用程序，在 12 个国家都可以听到“jodelling”。

对于 Jodel 团队来说，每天处理成千上万的用户和管理数百个位置是一个关键的挑战。幸运的是，Node.js 正是为此而构建的；借助 Node.js 的可伸缩性特性，Jodel 每月可以消费和广播数百万条消息，而无需在基础设施上花费大量资金。说到基础设施，为了简化应用程序部署，Jodel 团队使用了 [Fightplan.js](https://github.com/pstadler/flightplan) ，这是由我们前面提到的同一个社区创建和维护的开源开发工具之一。

当然，还有更多。使用许多 Node.js 库和工具来扩展他们的平台，Jodel 成为了一个真正的 Node.js 高级用户。它本身当然不是一个目标；目标是让数百万人对该应用的性能感到满意。没有 Node 和社区，这是不可能的。

## node . js in education-meinaunterricht . de

总部位于柏林的教育技术公司 k.lab 是德国教具市场的领导者。从 2011 年开始，他们向全国大部分的 k-12 教师推出了他们的网络平台-meinterricht . de。k.lab 与主要出版商一起，将传统的纸质材料转换为数字和交互式材料。似乎这还不够，门户网站还提供了大量高质量的教育视频。该平台每天都有数千名教师使用，他们访问 meinUnterricht.de 来浏览其海量的教学内容。

为了处理和交付如此多的文档，k.lab 选择了 Node.js。他们基于节点的平台的主要用例是保持内容工作流的活力。这包括:版权处理、为多种设备优化内容、将内容上传到云端以及最终处理用户的下载。此外，k.lab 使用基于节点的 web 服务器 express.js 来运行 meinUnterricht 网站。它的作用是根据教师的订阅计划为教师提供内容。

如此复杂的应用程序需要与大量第三方工具如搜索引擎或订阅平台进行对话。幸运的是，将 Node 连接到其他服务很容易，因为供应商很乐意为最流行的技术之一提供插件。

## 电子商务中的 node . js-Grover

两年前，Grover 的创始人 Michael Cassau 踏上了一场变革我们购买电子产品方式的旅程。他的平台可以让你租一个设备。你没听错。你可以租用最新的 iPhone 或无人机，按需付费，如果你不再需要它，就可以归还。

虽然生意听起来很简单，其实不然。它需要在不同的系统之间交换信息，如 CRM、营销自动化、客户验证、库存管理、运输、报告等等。现在，电子商务服务使用 REST APIs 和 JSON 作为数据交换的主要格式。因为 JSON (JavaScript Object Notation)是 Node.js 的原生格式，所以它可以无缝地为 Grover 处理所有数据。

你可以看到，即使是像 Salesforce 或 MailChimp 这样的顶级 CRM 和电子商务公司也支持 Node.js 与专用软件开发工具包的集成。这使您可以将不同的系统粘合起来，更快地构建一个交钥匙解决方案。

## 大数据中的 node . js&图像处理- ShareIQ

正如你在栈溢出调查中看到的，JavaScript 也在数据工程类别中领先。ShareIQ 是一个很好的例子，说明了 JS 是如何做到这一点的。他们的使命是让营销人员完全控制他们的视觉营销投资。他们是如何做到的？

ShareIQ 开发了独特的技术来识别散布在互联网上的图像中的品牌资产。他们抓取并分析数百万个网站，以了解客户的营销资产是如何使用的。此分析的主要目标是发现影响者并减少欺诈。

从数十亿张图像中抓取、处理和创造洞察力需要终极性能。ShareIQ 团队已经证明 Node.js 再一次完美地适合了这样的用例。

## API-Voucherify 中的  Node.js

在回顾 Grover 案例时，我们提到他们的平台与几个营销自动化工具对话。 [Voucherify](https://voucherify.io?utm_campaign=tech&utm_medium=Link&utm_source=devto) 就是其中之一。Voucherify 背后的团队希望为营销团队提供一个推广基础设施，迄今为止，这种基础设施只保留给大型电子商务玩家。他们通过提供 dashboard 和 REST API 来帮助您构建和自动化优惠券、礼品卡和推荐活动。

有两个挑战:第一个是映射和自动化优惠券生命周期。这包括生成独特的代码，通过电子邮件或短信等各种营销渠道分发它们，在线和离线接受来自多个客户接触点的赎回，最后计算投资回报。秤带来第二个。当你处理数百万的代码时，你最好有一个高性能的系统。否则，当终端客户的代码在收银台不起作用时，他们很快就会失望。

Voucherify 使用 Node.js，因为 Node.js，正如我们从前面几节中了解到的，有助于解决这两个问题。

## 总结

我收集的案例表明 Node.js 可以成功地用于许多不同的业务领域。但是，是什么让我认为这些公司真的对 Node.js 感到高兴呢？Jodel、k.lab、Grover、ShareIQ 和 Voucherify 都是我们的客户，我们已经看到他们在幕后是如何做这些事情的。