# 动画:CSS 与 JavaScript

> 原文：<https://dev.to/hugo__df/animations-css-vs-javascript-3ko3>

### *如果可以使用 CSS，什么时候应该使用 JavaScript？*

这个帖子是由我在 Quora 上回答的一个问题引发的:
[**CSS 和 JS 哪个更好用？**](https://www.quora.com/Which-is-better-to-use-CSS-or-JS)

web 前端是由 HTML、CSS 和 JavaScript 三足鼎立构建而成的(详见[“后端代码、前端代码和它们如何交互”](https://hackernoon.com/in-simple-terms-backend-code-frontend-code-and-how-they-interact-2485c5a1bbd2)):HTML 说事物是什么，CSS 说它们看起来如何，JavaScript 做其他两者做不到的所有事情。

从这个角度来看，这个问题的答案是:“当你可以使用 CSS 的时候，你应该什么时候使用 JavaScript？”就是**从来没有**。

HTML、CSS 和 JavaScript 确实有重叠，尤其是自从 HTML5 和 CSS3 之后。CSS3 之前的动画曾经是 JavaScript 的前提，复杂的动画编排仍然是像 GSAP 这样的库，但现在我们也可以使用过渡 CSS 规则来制作动画。

这让我们制定了一些在浏览器中使用 JavaScript 的规则:

*   如果你能用 CSS 胜过 JavaScript，那就去做吧

*   如果你能通过 JavaScript 用 HTML 做一些事情，那就去做吧

## 为什么？

有几个原因可以解释为什么你应该尝试利用浏览器的 CSS 引擎，而不是滚动动画、聚焦状态等等。

这源于写代码尽量简单，但又不能太简单的原则。CSS 和 HTML 的声明性质使它们能够编写简洁、健壮和可维护的代码。因为 JavaScript 可以做这么多事情，打破这么多不同的方式，你写的越少，你遇到问题的可能性就越小。

## 1。弹性

CSS 和 HTML 竭尽全力自我补救。它们超级健壮:一个被破坏的 CSS 规则不会使你的整个网络应用崩溃，一个丢失的标签可能会破坏你的布局，但是除了在极端情况下，它不会阻止你的网站加载。

一个 JavaScript 语法错误或在使用字段之前缺少 null/undefined 检查(例如，当 myObj 为 null/undefined 时，myObj.something)将使整个应用程序崩溃，迫使用户刷新整个应用程序，并可能在此过程中丢失一些工作。

## 2。最佳化

因为 CSS 是声明性的，所以有更多的优化空间。浏览器有时可以使用硬件加速来做 CSS 的事情。

它还可以优化一些事情，比如不计算屏幕外元素的样式或运行它们的动画。

JavaScript 不能访问这些现成的 API。

## 3。容易出错且代价高昂

除了 JavaScript 的弹性不如 CSS 之外，即使是写得相对较好的 JavaScript 也可以做锁定 UI 甚至让浏览器崩溃之类的事情(尤其是低端的)。

有了 CSS，不兼容 CSS3 的浏览器，也就是那些你不再使用的浏览器，但是你的用户，可能会忽略转换规则，所以默认情况下*你会有优雅的降级。在 JavaScript 中，你必须检测用户运行的是旧的还是低规格的硬件，这使得它成为一个*选择进入*优雅降级…我知道我更喜欢哪一个。*

## 4。JavaScript 的成本

比起 JavaScript，更多的网站需要 CSS。这就是为什么尽管有“最佳实践”，许多开发人员(很多=“我对此感到内疚”)，仍然在头脑中加载他们的风格，即使他们在主体的末尾加载他们的脚本。

CSS 的解释成本也很低，因为它是声明性的，你可以并行创建内存中的样式表示(因为 CSS 中的每个选择器都可以并行解释)，也可以推迟最终样式的计算，直到元素被绘制出来。

JavaScript，因为它是一种通用编程语言……就像 CSS 必须被加载，但它也必须被解析和 JIT 编译以及运行。这意味着对于相同数量的 CSS 和 JavaScript，JavaScript 在硬件时间上花费更多。

## 5。维护成本和增加的依赖性

加载 JavaScript 动画库的代价有两种:

1.  您正在使用自己的 API 添加一个依赖项，这意味着对于那些希望在您之后维护代码的人来说，可能需要学习更多的东西

2.  您正在加载一个依赖项，增加了 JavaScript 加载/解析/运行的启动成本

CSS 动画和使用伪类检测 DOM 状态在加载和不产生依赖性方面都不会花费你什么，因为 CSS 和 HTML 是由 web 标准支持的。很可能任何新开发人员都会熟悉 CSS 动画，如果他们不熟悉，他们应该熟悉。

随时在 Twitter [@hugo__df](https://twitter.com/hugo__df) 和我联系。

如果你喜欢这个，留下一些♥️，这里有一些你可能会感兴趣的帖子:

*   [**为什么 CSS 很难**，我们不习惯的东西往往更难](https://medium.com/@hugo__df/why-css-is-hard-89f3b221dd4d)
*   [**学习 git 即使你不编码**，我们使用的工具定义了我们，git 是所有开发人员使用的工具](https://hackernoon.com/learn-git-even-if-you-dont-code-89c521f4209b)
*   [**你应该在 AWS** 上托管的 7 个理由，亚马逊网络服务是领先的托管提供商之一，下面是原因。](https://medium.com/@hugo__df/7-reasons-you-should-be-hosted-on-aws-9312e40ee323)

最初发表于 2018 年 1 月 21 日 codewithhugo.com。