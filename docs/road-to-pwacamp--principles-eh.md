# 通往#PWACamp /原则之路

> 原文：<https://dev.to/devfestnyc/road-to-pwacamp--principles-eh>

2017 年 7 月 22 日，我们将在纽约的大会上，通过谷歌开发团队 NYC 举办第一次聚焦于渐进式网络应用(PWA)的*学习营*活动。

> *您可以在* [*我们的活动页面*](https://pwacamp2017.splashthat.com/) *和* [*了解更多活动日程，点击*](https://generalassemb.ly/education/progressive-web-apps-camp/new-york-city/39343) *购票参加。* ***使用促销代码 pwa-30 限时获得 7 折优惠。***

该活动的主要目标是通过初学者快速启动 PWA 开发，并通过实践者巩固真实世界的部署。作为 [Deep Work](http://calnewport.com/books/deep-work/) 的粉丝，我认为像这样为期一天的活动对于增强将意识转化为行动的沉浸式学习方法至关重要。

### [T1】# pwa camp 系列](#pwacamp-series)

在接下来的几周里，我希望发布一系列的帖子，为参加#PWACamp 的人提供背景和资源，并希望走得更远。随着帖子的推出，该列表将更新相关文章的链接..

1.  [通往 PWA 营地的道路/原则](https://medium.com/pwa-camp/road-to-pwacamp-principles-cf1a23886964)
2.  通往艾滋病毒/艾滋病营地/方案的道路
3.  通往艾滋病毒感染者营地/实践的道路
4.  …

### #PWACamp 议程

该活动分为三个部分——“T0”原则、*编程*和*实践——“T5”——从了解核心概念&工具，到获得构建 PWA 的实际开发经验，最后从行业专家那里了解实际部署。*

<figure>[![](img/7a7e09521df3060b5bf4964d35cc3cfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vuHIy4fg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/839/1%2AJj132T49N7jCLrbzNizcAg.png) 

<figcaption>PWA 营 2017 演讲嘉宾</figcaption>

</figure>

### **#PWACamp 第一场:原理**

其核心是，渐进式 web 应用程序愿景是使用*渐进式增强*来提供一种 Web 应用程序体验，这种体验是*可靠的*(即使在恶劣的网络条件下也能正常工作)*快速的*(快速开始绘制、平滑的动画和对用户事件的响应)，以及*引人入胜的*(感觉像一个具有沉浸式全屏 UX、熟悉的启动选项&推送通知的原生应用程序)。

> [Pete le page(Google 的开发者倡导者)](https://www.twitter.com/petele)将在#PWACamp 开始，深入探讨 PWA，更详细地介绍这些概念。

它还非常重视安全性，将 [HTTPS 作为 PWA 合规性的核心要求](https://developers.google.com/web/fundamentals/security/encrypt-in-transit/why-https)。HTTPS 确保客户端和服务器之间的所有通信在默认情况下都是加密的，防止中间人攻击，同时还支持网站和用户数据的数据完整性。更重要的是，HTTPS 对于利用许多新的浏览器平台功能所需的权限工作流至关重要，这使得开发人员了解 HTTPs 如何工作以及它如何用于 PWA 变得至关重要。

> 真理子·小坂将在她备受期待的#PWACamp 演讲“在 PWA 中采用 HTTPS”中，分解 HTTPS 的复杂性&

但是 PWA 是一个构建更好的网络应用的愿景，而不是一个新的开发框架或平台。相反，目标是找到在各种前端技术(如 React、Angular、VanillaJS、Polymer、Ember、Vue 等)中整合和验证最佳实践的方法。).做好这一点需要工具支持——不仅仅是为了构建 PWA，也是为了根据性能和可访问性等各种指标审核它们的合规性。

2017 年 5 月，Chrome 团队发布了 [WorkboxJS](https://workboxjs.org/) ，这是一个 JavaScript 库工具包，可以与流行的构建工具无摩擦地工作，以实现核心 PWA 功能。他们还宣布将[项目 Lighthouse](https://github.com/GoogleChrome/lighthouse) 集成到 Chrome 开发工具中，使得使用与开发和调试相同的界面来审计你的 web 应用变得直观。与 PageSpeed Insights 一样，Project Lighthouse 审计也是规范性的——它不仅根据 [PWA 清单](https://developers.google.com/web/progressive-web-apps/checklist)识别问题，而且还提供了关于如何解决问题的清晰指导，以便即使在需求&功能发生变化时也能获得合规性。

*杰夫·波斯尼克(Dev Rel @Google)将在#PWACamp 重访他在 PWA* *上的 Google IO17 会议* [*，特别关注开发人员对 WorkboxJS & Lighthouse 的使用*](https://www.youtube.com/watch?v=fyi7auD5MzU)

### 为什么要参加#PWACamp

如果你在行业中管理团队或监督项目，那么 2017 年是 PWA 翻倍的一年。《福布斯》将 PWA 列为 2017 年最值得关注的网络趋势之一。 [PWA 是 Google IO](https://developers.googleblog.com/2017/05/google-io-2017-empowering-developers-to.html)(2017 年 5 月)的一大话题，许多[高调的 PWA 发布会](https://developers.google.com/web/showcase/)展示了 PWA 的大规模采用。

对于 web 开发人员来说，Google IO 的标志是发布了 WorkboxJS，宣布 Lighthouse 集成到 DevTools 中，以及推出了[HNPWA](https://hnpwa.com/)——todom VC 的精神继承者——现在为开发人员提供了使用各种前端技术实现黑客新闻客户端的 PWA 的全面示例。

而且势头一直在增长..

无论你是 PWA 新手还是已经使用了一段时间，这都是了解 PWA 前沿最新发展的好方法。更重要的是，这是一种在 PWA 开发者、从业者和用户社区中建立联系的方式

在下一期“**通向#PWACamp:编程之路”**中，我们将探讨如何使用通用平台(例如，与 Glitch 重新混合)或特定前端技术(例如，React、Angular、Polymer、Vue 等)开始 PWA 开发。).

敬请关注。

### 要看的视频/ PWA @GoogleIO 2017

1.  [渐进式网络应用:无处不在的美好体验](https://www.youtube.com/watch?v=m-sCdS0sQO8&index=5&list=PLNYkxOF6rcICniLJ2rfj0FexlA-9zmJJE)、[萨姆·伯奇](https://medium.com/u/2e5570e0a783)和[亚历克斯·罗素](https://medium.com/u/fa63e1e8071)
2.  与 Angular 、 [Stephen Fluin](https://medium.com/u/a1fba6cea10f) 和 Alex Rickabaugh 的精彩 PWA 体验
3.  [WomenTechmakers.com:一位 PWA 移民](https://www.youtube.com/watch?v=fyi7auD5MzU&list=PLNYkxOF6rcICniLJ2rfj0FexlA-9zmJJE&index=17)、[杰弗里·波斯尼克](https://medium.com/u/3fb739097395)和[埃瓦·加斯佩罗维奇](https://medium.com/u/625272420a5a)
4.  [采用 JS 框架的生产 PWA](https://www.youtube.com/watch?v=aCMbSyngXB4&list=PLNYkxOF6rcICniLJ2rfj0FexlA-9zmJJE&index=24)， [Addy Osmani](https://medium.com/u/2508e4c7a8ec)
5.  远离礁石:使用灯塔建造适于航海的 PWA 、[埃里克·比德尔曼](https://medium.com/u/443b43fab64)和[布伦丹·肯尼](https://medium.com/u/367f15fa8a7f)
6.  [拿到绿色的锁:来自田野的 HTTPS 故事](https://www.youtube.com/watch?v=GoXgl9r0Kjk&list=PLNYkxOF6rcICniLJ2rfj0FexlA-9zmJJE&index=20) [艾米丽·谢克特](https://medium.com/u/10b89e6593cd)
7.  [用 PWA](https://www.youtube.com/watch?v=mmq-KVeO-uU&list=PLNYkxOF6rcICniLJ2rfj0FexlA-9zmJJE&index=19) 、[欧文·坎贝尔-摩尔](https://medium.com/u/94a116f73676)创造出感觉恰到好处的 UX

* * *