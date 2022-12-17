# 每周网络综述- 01

> 原文：<https://dev.to/ardennl/weekly-web-roundup---01-15d6>

新的一年刚刚开始，一切都已经着火了🔥。幸运的是，除了我们 CPU 的致命弱点之外，还有更多的问题在发生。在这篇文章中，我收集了 2018 年第一周以来我最喜欢的关于网络开发的文章、推文和想法。尽情享受吧！

## 新闻

### 彻底垮台&厉鬼

不要慌！Meltdown 和 Spectre 并不是你需要学习的新 JavaScript 框架。这些漏洞似乎会影响自 1995 年以来制造的几乎所有 CPU。你现在可能会恐慌。

研究人员发现的漏洞可能被用来拦截密码、登录和其他凭证...无影无踪！目前你能做的不多，但要确保你的软件是最新的，并关注事态的发展。无论如何，Meltdown 和 Spectre 将在未来很长一段时间内产生影响。积极的一面是，Meltdown 和 Spectre 的标志非常酷。

## 文章

### 生产力

#### 与戴夫·格迪斯一起处理日常事务

在 Twitter 上，编程大师 Kent C. Dodds 分享了一篇关于常规以及如何保持常规的文章。这是由[戴夫·格迪斯](https://twitter.com/geddski)写的，你可能从他的 [Flexbox 僵尸](https://flexboxzombies.com/p/flexbox-zombies)和[网格生物](https://gridcritters.com/p/gridcritters)教程游戏中知道他。无论如何，戴夫有几点真的引起了我的共鸣，比如当你脑子里还想着互联网时，按时上床睡觉有多难:

> 我的新习惯是每天晚上 10 点开始(一个成功的早晨习惯是在前一天晚上开始)。当晚上 10 点左右的时候，我几乎不想关掉我的大脑/电脑，开始准备睡觉。

现在我试着读一本(小说！)睡觉前看书。这让我可以专注于与工作无关的事情，副作用是让我昏昏欲睡。

你有什么惯例来确保你第二天精神饱满地开始吗？

#### 生产力链接

*   如果你正在寻找更多的建议， *Smashing Magazine* 社区在[由](https://www.smashingmagazine.com/2017/12/community-productivity-tips-tricks/) [Rachel Andrew](https://www.smashingmagazine.com/author/rachel-andrew/) 撰写的这篇精彩文章中分享了他们所有关于如何高效的最佳建议。

### 做出反应

#### 用 DriveTribe 升级反应 16

如果你真的对汽车感兴趣，你可能知道 Drivetribe，一个关于...汽车。我对他们的技术更感兴趣，因为他们似乎对自己的发展非常认真。Matt Perry，drive-tribe 工程师， [Popmotion](https://popmotion.io) 的创造者和全能的聪明人，写了一篇关于将 DriveTribe 堆栈升级到 React 16 的伟大文章，事实证明这非常容易，尤其是如果你知道 React 路由器 v3 实际上是兼容的。阅读 [DriveTribe 工程博客](https://medium.com/drivetribe-engineering/an-unravelling-tale-of-performance-bonus-round-react-16-f98455fdabe6)上的文章。

#### 反应过来的链接

*   NPM 发布了他们的 2017 年 JavaScript 框架统计数据。这些数字可能不会让你吃惊
*   DriveTribe 还发布了一个 [React 路口观察者](https://github.com/drivetribe/react-intersection)插件。看起来真不错。
*   在 web 开发中，一年就像几十年。尽管如此，我觉得 musefind 工程师的 React 组件编写最佳实践指南有一些很好的建议。
*   根据大众的要求，现在 react 指南中有一篇关于代码拆分的[文章。](https://reactjs.org/docs/code-splitting.html)

### 可达性

#### 劳拉·卡尔巴格的《每个人都有机会》

我终于有时间读完了劳拉关于无障碍的书。这是一个很好的资源，尽管它有点枯燥(但哪本科技书不是)，但它写得非常清楚，几乎是如何让你的应用和网页包容的完整指南。对我来说，这本书似乎是每个发展机构的必备书籍之一。[为每本书上的每个人购买可访问性](https://abookapart.com/products/accessibility-for-everyone)。

[我还在书上做了一些笔记](https://github.com/aderaaij/book-notes/blob/master/accessibility-for-everyone--laura-kalbag/index.md)

#### 辅助功能链接

*   默认情况下表是可访问的吗？显然不是！[和斯蒂芬·朱迪斯](https://www.stefanjudis.com/today-i-learned/the-for-accessibility-required-caption-element-in-html-tables/)一起了解`caption`元素
*   [MDN web docs 也有一些关于可访问性的重要信息](https://developer.mozilla.org/en-US/docs/Learn/Accessibility)
*   [艾米丽·米尔斯](https://twitter.com/ermmears)写了一篇关于 React 和可访问性的[文章](https://medium.com/@emilymears/getting-started-with-web-accessibility-in-react-9e591fdb0d52)

### 通用

*   在跨原点 iframes 和低功耗模式下，iOS 将 requestAnimationFrame 限制为 30fps。[在 popmotion 博客上](https://popmotion.io/blog/20180401-when-ios-throttles-requestanimationframe/)
*   关于这一点，如果你还没有尝试过 Popmotion，你应该试试！这无疑是自格林斯托克以来我合作过的最令人印象深刻的动画库。
*   Scrollarama 是一个基于十字路口观察者的卷轴式故事库。由于交叉点观察器 API 非常简单，我不确定你是否需要一个用于 htis 的库，但它看起来不错。(此外，交叉点观察器和基于时间的动画不是有一些性能问题吗？)
*   Zell Liew 发布了他的 [JavaScript 课程“learn JavaScript . today”](https://learnjavascript.today/)，该课程将试图为前端开发人员彻底去除 JavaScript 的迷雾
*   WordPress 社区似乎总是为这样或那样的事情而大动干戈。目前的主题是古腾堡，即将到来的(？WordPress CMS 的编辑器。Webdev studios 写了一篇关于古腾堡如何影响你当前内容的文章。
*   Smashing Magazine 发布了一个很大的[前端性能清单](https://www.smashingmagazine.com/2018/01/front-end-performance-checklist-2018-pdf-pages/)而且很牛逼。
*   一个新的 JavaScript 框架？再来一杯没关系。svelite 是新上市的产品，看起来非常简单方便。我还没试过。
*   差点忘了，但是莎拉·德拉斯纳制作了这个神奇的 JS [对象资源管理器](https://sdras.github.io/object-explorer/)来帮助你找出应该应用的方法。太神奇了。

## 我一直在忙什么？

### 阅读凯尔·辛普森的“功能轻 JavaScript”

《凯尔·辛普森一家》 [【你不知道 JavaScript】](https://github.com/getify/You-Dont-Know-JS)系列对我深入理解这门语言有很大帮助。因为我没有使用过任何其他编程语言，所以我不知道 JavaScript 有点奇怪。没有一种*方法可以写出好的 JavaScript，有许多概念和范例可以应用于这种语言。凯尔·辛普森的《函数式轻量级 JavaScript》似乎是我所需要的函数式编程入门，到目前为止这本书非常有趣。这本书在 Leanpub 上出版，但也可以在 Github 上免费获得。别忘了给凯尔一些支持！*

### ES6 类

谈到范例；我想写一篇关于 ES6 类的简单文章，因为看起来很多人使用它们只是因为它们是框架自带的。我一点也不知道我会掉进兔子洞，现在我也在写关于范例、一般原型继承的文章，并试图在 JavaScript 的支持和反对阵营之间找到一些中间地带。

### 盖茨比首发

众所周知，我是 Gatsby 和基于 React 的静态站点生成器概念的粉丝。更多的是为了我自己，而不是其他任何人，我做了[Gatsby skeleton starter markdown](https://github.com/aderaaij/gatsby-starter-skeleton-markdown)来总是有一个 SEO 友好的 PWA 作为我的 web 应用程序的起点。

### 读《成事》...又

上一次我在读*成事* (GTD)的时候，读到一半我就退出了，对于一本声称能帮你成事的书来说，这可不是个好兆头。但我向你保证，我确实从中学到了很多。过了一段时间，这本书给了我一种焦虑，好像我需要不断地分类、组织和构建我生活中的每一步。我不认为生活是，或者应该是，那么刻板。无论如何，我要再试一次。

## 淋浴思绪

### 多读小说

为什么每次开发者推荐一本书时，都是“成为高效忍者的 12 种方法？”。小说更有趣、更轻松。它们就像你最喜欢的电影和电视节目，只是没有屏幕和焦虑。我非常喜欢的一个系列丛书是西尔万·纽维尔的《Themis files》。我喜欢现代作家的一点是，我可以在 twitter 上纠缠他们，他们会回答！

### 一切都坏了

如果网络“坏掉了”，应用程序更是如此！

> 想变老吗？
> 
> iPad 上的猴岛 2 高清翻拍版已经不行了，大概是 8 年前出的，已经不兼容了。🏴☠️
> 
> — Thomas "Kick Nazis out, [@jack](https://dev.to/jack) " Fuchs ([@thomasfuchs](https://dev.to/thomasfuchs) ) [January 2, 2018](https://twitter.com/thomasfuchs/status/948003372795342849?ref_src=twsrc%5Etfw)

### React 团队真的很酷

虽然我非常不喜欢脸书这个概念，但我喜欢 React 和他们的核心团队。我对他们的参与感到惊讶，即使这是一个拥有无数插件和改编的巨大的库，你可能仍然会遇到一个 React 核心成员在一个 5 星的库中回复 Github 问题。👏

## 鳍

在这一点上，我将结束这一通讯！我希望你喜欢我的网络周。如果这篇文章被欣赏，我会试着每周或每两周发表一些东西。此外，任何关于格式的建议都是非常受欢迎的。