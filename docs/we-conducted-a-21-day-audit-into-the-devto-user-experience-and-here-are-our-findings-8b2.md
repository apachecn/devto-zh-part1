# 我们对开发人员到用户的体验进行了为期 21 天的审计，以下是我们的调查结果

> 原文：<https://dev.to/darjun0812/we-conducted-a-21-day-audit-into-the-devto-user-experience-and-here-are-our-findings-8b2>

### 通过重构信息层次结构改善用户体验

***概述*** *: 21 天，团队项目(3 名成员)，用户研究，包括筛选、用户访谈、可用性测试(3 轮)、竞争/比较分析、启发式评估、草图绘制、线框图、人物角色、用户/任务流程、用户旅程、快速原型制作、利用草图和视觉的高保真实体模型和原型。*

### **范围**

dev.to 是一个在线社区，程序员可以在这里学习、分享想法和寻找机会。dev.to 诞生于受欢迎的 Twitter 账户 [ThePracticalDev](https://twitter.com/ThePracticalDev) ，致力于创建一个分享和发现伟大想法、进行建设性辩论和结交朋友的在线社区。任何人都可以分享文章、问题、讨论等。只要他们有权分享他们的话。Practical Dev 找到我们在 General Assembly 的团队来重新设计他们的网站，以改进当前的入职和注册流程以及网站的整体可用性，同时鼓励更多用户参与社区。经过彻底的研究和测试后，我们专注于改进信息架构和层次结构，以改进整个网站的功能和流程，同时保持创始人保持网站轻量级的目标。

### **研究**

我的团队评估了各种业务目标，包括增加用户注册和帐户创建率，增加活跃用户率，改善入职流程以及网站功能和布局的整体改善。基于我们的假设和业务目标，我们以一个初始问题开始了我们的研究阶段:**我们如何改进当前的入职和注册流程，同时鼓励用户参与在线开发社区？**

**启发式评估**

使用 ABBY 方法，我们评估了 dev.to 的各种因素，如可发现性、可学习性和可访问性。

目标:我们的目标很简单，我们想弄清楚网站的整体可用性是什么样的，以及用户的一些痛点可能是什么。

*见解*:我们发现网站上的信息层次需要改进，功能可发现性需要提高，网站没有尽可能地可控。

**竞争/比较研究**

考虑到我们的问题陈述，我们决定进行竞争和比较分析，以了解哪些相关网站做得很好，以及 dev.to 如何实现上述优势。我们看到的网站例子有 Stack Overflow、Reddit 和 LinkedIn。

一些网站集成了 dev.to 上没有的简单功能。这些功能包括搜索结果的组织和分类方式、可用内容的多样性以及帖子在界面上的组织和分隔方式。

<figure>[![](img/e9450b772f24dc5a5cada1116c9ca915.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YT_xob1---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/512/1%2A0AOreKb9dly-2AWZuTp_gw.png) 

<figcaption>带建议和帖子的中型登陆页面。</figcaption>

</figure>

**用户访谈和初步测试**

我们采访了 24 位用户。该小组包括学生和专业开发人员，从不熟悉 dev.to 网站的人到该网站的高级用户。我们最初的问题集中在背景上，当人们想知道一个问题的答案时，参考了哪些资源网站，人们通常在网站上寻找什么功能(入职偏好、网站结构等)。).我们还要求用户在现有的 dev.to 站点上运行一系列任务，让我们知道他们在运行这些功能时在想什么。用户反馈包括如下回应:

“我希望在入职期间接受培训”

“我喜欢干净简单、易于导航的用户界面”

“我想立即注册的内容”

“我不想永远滚动”

***合成***

<figure>[![](img/d26efb7d229c76c8585f97964e8b0d61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JhirJv7v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATa_G81qoUUT9kcmdC7IN8g.jpeg) 

<figcaption>用户访谈回复要点</figcaption>

</figure>

*:我们发现很多用户喜欢 dev.to 展示的社区，他们对选择点击的内容感到受欢迎和由衷的高兴。虽然对 dev.to 正在构建的东西的热爱是显而易见的，但很多用户对网站的各个部分都感到沮丧。该网站最初的问题是，他们的大多数用户群都是通过 Twitter 或谷歌搜索点击帖子，而不是通过主页进入，因此错过了该景点所展示的许多功能。当用户真正查看主页时，呈现给他们的是一个超级混乱的界面。我们发现用户对主页的结构不满意，他们觉得界面上有太多的东西，一些功能被隐藏了(因此他们不知道),而且没有特定的信息结构来立即呈现或显示为搜索结果。即使是从网站启动以来就在网站上的用户也对网站的外观和分类方式不满意。视觉很重要，结构也很重要，用户注意到如果网站对他们的体验更友好，他们会更多地使用它！*

 *### 人

根据我们的综合，我们创建了两个主要人物角色，并为我们的第一个人物角色规划了一个初始用户旅程:

[![](img/0bf653f4869de8e2daf2fa849aa0264e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rMX5o-da--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYZjSU7Vd2_rR2S1pkWvIEg.png)

[![](img/c66a3f5c41d50c2653410d0fee77c6b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hTmpyxWZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGaraty80JDjTTFwjhlPxtw.png)

[![](img/3ecd6c03e950b67980e8118fcb045754.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JacSg8uW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdYz1mp8qDCiBJk7EFQvYHg.png)

<figure>[![](img/d2fa4cec6ddc89e956a7cc62df8266d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wMeM8mIJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVVi3UDeD8y0Hl9jF0rG7-Q.png) 

<figcaption>用户语录和痛点来源于访谈、测试和综合</figcaption>

</figure>

**修改后的问题陈述**

在综合了我们进行的所有研究后，我们发现了一些关于当前开发站点的明显问题，并修改了我们的问题陈述:

**我们如何通过改善当前网站的信息层级来加强社区参与开发？**

### 将研究转化为设计

在重组了我们的问题陈述之后，我们进入了设计阶段。dev.to 希望对他们的站点做一些更改，同时尽可能保持站点的轻量级。一旦我们确定了重组网站的信息架构对用户有益的想法，我们就把注意力集中在可以立即实现的变化上。这些变化基于我们在研究过程中发现的见解:

<figure>[![](img/c56ed3232e924c321dc3a9d1735fdd60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EO_P3qE3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfJq15mL3HLNsGfrJcowmtg.png) 

<figcaption>洞察导致设计变化</figcaption>

</figure>

[![](img/2e229868c61d7a7346e0041a967f2fbf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9BnNzi-C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6UwOB_hdON0na1Js7DnM7g.jpeg)

[![](img/215b1233adacb33ea0a79bd4b4d502af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NuAvDDc4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtKkghiB18fOZ-v0NYUCEtQ.jpeg)

<figure>[![](img/1f696657618975d3e7e514f811d51572.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1GBejq3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0ph1Fuij5-EpzGjabIkm3w.jpeg) 

<figcaption>进入我们的原型设计工作室</figcaption>

</figure>

<figure>[![](img/6736995c565684e35fe3b984fdceaa55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dq7Dplmi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0mfHS2rHjSAGzb8iA_IleQ.jpeg) 

<figcaption>初始筛选思路</figcaption>

</figure>

### 原型

你可以在这里查看我们的原型:

[点击此处查看项目`dev.to prototype`](https://projects.invisionapp.com/share/G4E1KCA75#/screens/255146195_Identify_Yourself)

请记住，上面的原型并不是一个全功能的程序。所有功能都旨在突出介绍新的入职和注册流程以及一些其他重新设计的功能。一些需要注意的关键界面是:新的注册流程、新的入职流程、重新设计的主页/用户界面、重新设计的用户资料等等！

### 测试回合 2

我们进入了用户测试的第二阶段，要求用户测试我们的原型。我们测试了 5 个用户，每个人都经历了 4 种不同的场景:

1)完成新的注册流程(早期介绍标签系统)
2)完成(可选)入职流程
3)通过通知提示完成用户资料设置
4)搜索标签，从搜索结果中点击进入文章，查看评论部分

我们的测试目标:

*   **用户在注册时应该明白 dev.to 是怎么回事**
*   **用户应该了解标签的用途**
*   **用户应该了解如何访问他们的个人资料和仪表板**
*   **用户应该能够过滤并轻松访问搜索结果**
*   **用户应该能够区分评论和回复**

<figure>[![](img/f28c3bba7c7ec7f35cadbb4e7af20374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tfjc2qdk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AewOT2UlXnK2ufG0xfVH-Zg.jpeg) 

<figcaption>用户正在体验我们新设计的入职流程</figcaption>

</figure>

**结果**:

[![](img/25afcf90c0d485b1706d55c074edf9aa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9qUXxJJH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aw_9vIyZrGmwT9V5hc2YDnQ.png)

**修改后的用户旅程**

[![](img/387326955f746e62b1d81d45b608c908.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eiyJUGnI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjANrx1jzXCP5MNttlZvTxw.jpeg)

洞察 :丹尼斯对这个过程更满意。他能够根据需要完成任务，并且更容易了解网站及其功能。

### **结论**

总的来说，我们的研究为我们提供了一些见解，包括层次结构、重组和呈现内容的重要性。我们看到，内容的重组和呈现提供了足够强大的好处，从而避免了立即进行彻底检查的需要。用户在第一次体验这个界面时，需要更清楚的东西在哪里，他们做了什么。清理用户界面和呈现快速的入职流程是关键，用户对最终产品更满意。有时候少即是多，做这些小的改变将会对 dev.to 产生很大的影响，并且代表了进一步迭代的良好起点。

### **进一步的步骤以及如何参与**

我们正在嵌入 dev.to 团队，并对更多的可用性测试和面试持开放态度。你对你的经历有什么要说的吗？或者对改进有什么建议或者你想看的东西吗？请联系我们，让我们知道你的想法！

<figure>[![](img/f2625e6e355d7e770de531589e104b40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QjOSlhyY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYIRGQib1Kmir0F-WsXXlAg.jpeg)

<figcaption>uxa 战队！</figcaption>

</figure>*