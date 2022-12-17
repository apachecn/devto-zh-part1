# 软件工程师的 10 大系统设计面试问题

> 原文：<https://dev.to/fahimulhaq/top-10-system-design-interview-questions-for-software-engineers>

<sup>*注:本帖原载于[黑客月刊](https://hackernoon.com/top-10-system-design-interview-questions-for-software-engineers-8561290f0444)。*</sup>

设计大型分布式系统已经成为软件工程面试的标准部分。工程师很难进行系统设计面试(SDI)，主要是因为以下两个原因:

1.  他们缺乏开发大型系统的经验。
2.  空间数据基础设施的非结构化性质。即使是有一些构建大型系统经验的工程师也对这些面试感到不舒服，主要是因为没有标准答案的设计问题的开放性。

在 SDIs 中的出色表现是非常值得的，因为它反映了你在复杂系统中工作的能力，并转化为面试公司将提供给你的职位和报酬(工资和股票)。

> 如果你正在寻找准备系统设计面试的资源，看看[正在寻找系统设计面试](https://www.educative.io/courses/grokking-the-system-design-interview)。

在 [Educative.io](https://www.educative.io) ，我们与数百名通过设计面试的候选人进行了交谈。作为流程的一部分，我们编制了一份最常被问到的系统设计面试问题列表。
以下是最常被问到的问题，以及一些面试官希望你在设计系统时考虑的事情。

## 1。设计 TinyURL 或 bitly(一个 URL 缩短服务)

[![Bitly + TinUrl logos](img/bf48df46fa9ddfc30d1ea9ae0a8e115a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fyWgTMRI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uv8cujjwemxf1kg9b78a.png)

给定一个(通常)很长的 URL，你将如何设计服务来为它生成一个更短且唯一的别名？
讨论以下问题:

*   如何为每个 URL 生成唯一的 ID？
*   如何大规模生成唯一的 id(每秒钟有数千个 URL 缩短请求)？*您的服务如何处理重定向？
*   你如何支持自定义短网址？
*   如何删除过期的网址等。？
*   如何跟踪点击统计？

## 2。设计 YouTube、网飞或 Twitch(一种全球视频流媒体服务)

[![Youtube + Netflix + Twitch logos](img/6921d23e333f4679f999ce23eb6a0737.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zHii0AEe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uhxico28g50e3lt5kjtp.png)

视频意味着您的服务将存储和传输千兆字节的数据。您应该讨论如何高效地存储和分发数据，以便大量用户可以同时观看和共享(例如，想象一下将热门电视节目《权力的游戏》的最新一集进行流式传输)。

此外，讨论:

*   你如何记录视频的统计数据，例如，总浏览量、投票数等。
*   用户如何在视频上添加评论(实时)。

你可以看看我之前的帖子[“如何在你的 45 分钟系统设计面试中不设计网飞”](https://dev.to/fahimulhaq/how-not-to-design-netflix-in-your-45-minute-system-design-interview)

## 3。设计 Facebook Messenger 或 WhatsApp(一种全球聊天服务)

[![Facebook + WhatsApp logos](img/11a8e0feedd4b098c5e104b67893f5a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X-M-hr40--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0hhwlel4huedvzun9hbt.png)

面试官想知道:

*   你会如何设计用户之间一对一的对话？
*   你将如何扩展你的设计来支持群聊？
*   当用户没有连接到互联网时该怎么办？
*   何时发送推送通知？
*   你能提供端到端的加密吗？怎么会？

## 4。设计 Quora 或 Reddit 或 HackerNews(一个社交网络+留言板服务)

[![Quora + Reddit + Hackernews logos](img/b7ac8d147d23f0460f5d6d8053e0e1d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aIIJDjlM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nzdgwixl6mzo76jrzu0k.png)

这些服务的用户可以发布问题或分享链接。其他用户可以回答问题或对共享链接发表评论。该服务应该能够:

*   记录每个答案的统计数据，例如总浏览量、投票数等。
*   用户应该能够关注其他用户或主题
*   他们的时间表将由所有用户的热门问题和他们关注的主题组成(类似于新闻提要生成)。

## 5。设计 Dropbox 或 Google Drive 或 Google Photos(一种全球文件存储&共享服务)

[![Dropbox + Google Drive + Google Photos logs](img/f1aa427f7bc68d831eb413cb1a933365.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--80-IW2z---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t4j9jt3z6aq3nw8dw8sr.png)

讨论以下问题:

*   用户如何上传/查看/搜索/共享文件或照片？
*   您如何跟踪文件共享的权限
*   如何允许多个用户编辑同一个文档

## 6。设计脸书、推特或 Instagram(拥有数亿用户的社交媒体服务)

[![Facebook + Twitter + Instragram logos](img/1dfa565a4d9ad5c3c429f0f61d5bab56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gPt2DfCj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/620wuupaf4yfm8xzqghd.png)

当设计一个拥有数亿(或数十亿)用户的社交媒体服务时，面试官很想知道你会如何设计以下组件

*   帖子或推文的高效存储和搜索。
*   新闻源生成
*   社交图(谁和谁成为朋友，或者谁关注谁——尤其是当数百万用户关注一个名人的时候)

很多时候，面试官会花整个面试时间来讨论新闻提要的设计。

## 7。设计优步或 Lyft(拼车服务)

[![Uber + Lyft logos](img/8287402bd1c998d3a655c69d93caa54b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nRLH1yp6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/52k8njw675wi62w3q8ud.png)

设计拼车服务时，讨论以下问题:

*   最关键的使用案例——当客户请求搭车时，如何高效地将他们与附近的司机匹配起来？
*   如何为总是在移动的司机和骑手存储数百万个地理位置？
*   如何处理司机/骑手位置的更新(每秒数百万次更新)？

## 8。设计一个网络爬虫或预输入(搜索引擎相关服务)

[![Google + Bing logos](img/09c748721c01f293b7fe57b27c4f6ba0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3M3Nv26D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3sb49bhq4b2xx3fcyp2b.png)

对于 Type-Ahead，当用户在查询中键入内容时，您需要设计一个服务，从用户键入的任何内容开始推荐前 10 个搜索项。讨论以下问题:

*   如何存储以前的搜索查询？
*   如何保持数据的新鲜？
*   如何找到与已经键入的字符串的最佳匹配？
*   如何处理更新，用户打字太快？

对于网络爬虫，我们必须设计一个可扩展的服务，能够抓取整个网络，并且能够抓取数以亿计的网络文档。讨论以下问题:

*   如何找到新的网页？
*   如何对动态变化的网页进行优先级排序？
*   如何保证你的爬虫不会无限卡在同一个域上？

## 9。设计一个 API 速率限制器(例如，用于 Firebase 或 Github)

[![Firebase + Github logs](img/eaa2c0386a73bad172f7f5d0af4a92a6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bB_h427N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xhs4sxr601jgyfdwzpuu.png)

您需要开发一种限速器服务，它可以:

*   限制一个实体在一个时间窗口内可以发送给 API 的请求数量，例如每秒 15 个请求。
*   速率限制应该适用于分布式设置，因为可以通过服务器集群访问 API。
*   你如何处理节流(软节流和硬节流等？).

## 10。设计 Yelp 或附近的地方/朋友(邻近服务器)

[![Yelp + Facebook Nearby logos](img/153bfe8991355bbb69ddf71d9f8ddb49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gZ3gcl5B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w58um04fnhq3apu5gyhs.png)

这项服务需要存储数百万人/地方的位置。讨论以下问题:

*   该服务的用户如何能够搜索附近的朋友或地方
*   如何排名(根据距离，用户评论)。
*   如何根据人口密度有效地存储位置数据(例如，纽约市的一个街区可能比一个小城市有更多的地方/人)。

## **软件工程师面试准备资源**

以下是一些可以帮助你准备软件工程面试的资源。

1.  **系统设计面试:** [钻研系统设计面试](https://www.educative.io/courses/grokking-the-system-design-interview)。
2.  **编码面试:** [探寻编码面试:编码问题的模式](https://www.educative.io/courses/grokking-the-coding-interview)。
3.  **延伸阅读:** [解剖系统设计面试](https://dev.to/fahimulhaq/anatomy-of-a-system-design-interview)

面试愉快！

* * *

法希姆是[教育机构](https://www.educative.io)的联合创始人。我们正在为软件工程师和教师构建下一代交互式学习平台。学习者通过互动课程来学习。教师可以使用我们的课程生成器快速创建和发布交互式课程。如果你有兴趣发布课程或了解更多，请随时联系。

* *所有产品名称、标识和品牌都是其各自所有者的财产。*