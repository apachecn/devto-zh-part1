# 关于棱角的 6 个常见误解

> 原文：<https://dev.to/mappmechanic/6-common-misconceptions-about-angular-4nbc>

## 简介

自 jQuery 以来，Angular 已经成为前端/UI 开发人员中最流行的词。Angular 是一个流行的基于 JavaScript 的开源前端开发框架，由 Google 和一个开发者/贡献者社区维护。

Angular 是由 miko he very 作为一个附带项目开始的，旨在供 web 设计人员使用，以便轻松地将可重用的实用程序添加到他们的项目中。他和另一个核心贡献者决定开源这个库。

过了一段时间，当 Hevery 开始在谷歌工作时，他说服了他的老板 Brad Green 在一个项目中使用 Angular。在那个项目成功之后，许多生产中的 Google Web 应用开始使用 Angular 作为他们的 UI 应用平台。

Angular 作为一个开源项目呈指数级增长，目前被大量尖端网站和移动 web 应用程序使用。

根据 https://www.madewithangular.com 不断增长的名单，像微软支持、纳斯达克、索尼电影、GoPro 和 Tesla 这样的受欢迎的组织在他们面向客户的门户中使用 Angular。Angular 还在帮助银行、金融和零售行业的多家企业集团快速开发内部企业应用程序方面发挥了巨大作用。

> 在本文中，我们将角度版本 1 称为 AngularJS，任何大于 2 的版本称为 Angular。
> 
> ## 流行的棱角分明

Angular 是 JavaScript 开发者社区中最受欢迎的模块之一，新的 Angular 核心 npm 模块在 2017 年 5 月已经被下载了 **1，459，312** 次，并且有多达 **2，634** 个其他模块依赖于它。AngularJS 和以上加起来在 Github 上有大约 **8 万**颗星。核心 Angular 项目的 GitHub 知识库已经联合了 **2，000** 名贡献者，这是开源项目中最大的项目团队之一。

Angular 已经成为一个受欢迎的选择，因为它有大量的社区支持、在线资源，如博客、文章和视频教程。由于 Angular 的流行，很多计划使用它的新开发人员对 Angular 有很多误解。

## 对棱角分明的误解

在这篇博客文章中，我想涵盖开发人员头脑中最常见的 6 个错误观念，并且我还将涵盖它的实际情况。

**1。棱角分明的是一座** **l** **的图书馆。**

一直在使用类似库的 jQuery 创建网站的 Web 开发人员经常觉得 Angular 或 AngularJS 只是另一个像他们一样的库。这是一个很大的误解，他们应该明白 Angular 是一个完整的框架，包含了构建现代单页 web 应用程序的设计模式和最佳实践。jQuery 是一个帮助编写长 JavaScript 函数的短语法的工具。

另一方面，Angular 就像一个工具箱，配备了大量的特性，包括:组件层次结构、模板、双向数据绑定、依赖注入、模块和响应支持。Angular 是一个完美的框架候选，足以构建一个从任何 web 服务器呈现的高性能 web 应用程序。

[![](img/c900db39186459fb76eb8dd6666c805d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---2H29yew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dl.dropboxusercontent.com/s/ylx7z4weudhtbqa/6-misconceptions-about-angular-jquery-vs-angular.png)

**2。Angular 是一个巨大的单体库。**

除了 Angular 甚至 AngularJS 开发人员之外，几乎所有使用框架的开发人员都认为新的 Angular 框架是一个庞大的整体。开发人员一直在创建 AngularJS 应用程序，作为具有共享范围和服务的大型臃肿应用程序，这些应用程序被认为会产生巨大的整体系统，这些系统已经成为许多组织的技术债务。

Angular 现在已经被分成多个模块，一个**核心**模块是骨架，主模块将它们缝合在一起。这些模块已经被划分，因此任何开发人员都可以根据自己的使用和环境选择加入或退出。可选模块有:@angular/animations、@angular/forms、@angular/router 和@angular/material。此外，Angular 背后的团队确实在这个框架中的模块系统背后投入了思考，该框架已被开发为 NgModules。角度文件中的定义是

> NgModules 有助于将应用程序组织成内聚的功能块。

NgModules 有一些高级功能，如运行时的惰性加载和依赖注入，这使它成为一个强大的工具，可以用模块化的方法制作大型分布式前端应用程序。延迟加载有助于在运行时异步加载模块，只要其他模块需要它。为了了解这方面的更多信息，你可以参考 https://angular.io/guide/ngmodule 的文档页面

**3。角度应用程序运行缓慢，不适合性能密集型用例。**

许多开发人员认为 Angular 应用程序比 React 或 Ember 应用程序慢，这成为他们为什么不尝试 Angular 的主要误解之一。许多使用这个框架开发的 web 应用程序要么被开发人员糟糕的实现所困扰，要么在核心框架中有一些糟糕的设计元素。

在新的 Angular 框架中，团队已经从最差的脏检查算法转移到单向流系统。他们将使用一个新的变更检测系统实现双向数据绑定，该系统使用一个名为 [zone.js](https://github.com/angular/zone.js/) 的新库。这些话似乎在嗡嗡作响，但它们都在引擎盖下，您可以在构建应用程序时使用开箱即用的双向数据绑定。Angular 自动使用区域来检测可能的更改以更新视图，并在内部实现一些 VM 来处理这些实现。

此外，新的 Angular 框架具有提前编译的特殊功能，这提高了 web 应用程序的性能。在旧版本的 Angular 中，要么是动态地从服务器请求模板，甚至是使用 JIT 编译模块，这降低了应用程序的响应时间。AOT 编译以及变化检测和通用服务器端渲染有助于新的 Angular 框架匹配 React 或 VueJS 的性能。

**4。角力** **打字稿** **的用法。**

另一个阻碍开发人员采用新 Angular 框架的误解是它倾向于 TypeScript。在我看来，这是它最大的优势，但 Angular 甚至可以在没有打字稿的情况下工作。我们可以使用 ES5 (JavaScript)来开发使用新 Angular 框架的应用程序。还有一个 [Dart](https://www.dartlang.org) 版本的 Angular，你可以在这个[导轨](https://webdev.dartlang.org/angular/)的帮助下使用。

Angular 这次是使用经过深思熟虑的设计模式开发的，这些设计模式有助于构建可伸缩和高性能的通用 web 应用程序。随着新的 ES6/ES7 特性的引入，JavaScript 正在发展成为一种更成熟的语言，比如:类、箭头函数、扩展操作符等。TypeScript 只是 JavaScript 的一个超集，为 JavaScript 增加了强类型的能力。它有助于在开发阶段识别和解决许多与类型相关的错误。虽然不用 TypeScript 也可以创建 Angular 应用程序，但我强烈建议使用它。关于这一点的一个很好的阅读是[这里](https://vsavkin.com/writing-angular-2-in-typescript-1fa77c78d8e8)。

**5。Angular 没有状态管理，或者与 Flux 或 Redux 不兼容。**

Web 应用程序每天都在变得越来越复杂，然而开发人员需要快速迭代和发布新功能，而不牺牲性能。对于任何复杂的前端应用程序，状态管理都非常重要。我们需要管理多种类型的状态，如服务器响应数据、本地 UI 状态、应用程序状态和用户偏好。AngularJS 不能很好地与外部状态管理库一起工作，但是新的 Angular 框架已经以可选地支持这些库的方式构建。像 redux 这样的状态管理库的主要概念是基于单向数据流的，但是 AngularJS 的默认双向数据绑定并不适合它。另一个问题是，在 AngularJS 中，理想情况下应该使用服务来管理状态，但是数据绑定是使用$scope 来完成的，因此在控制器中将这些绑定在一起变得很困难和麻烦。

新的 Angular 框架在其核心遵循单向数据流设计模式，这使得它易于与流行的状态管理库(如 Flux、Redux、RxJS 或 ImmutableJS)一起使用。由于良好的变化检测策略，即使不使用这些外部库，也可以在公共服务中使用状态，并将它们注入到 Angular 模块中。关于这个主题的一个令人惊奇的阅读资源可以在这个链接获得:[https://bertrandg . github . io/angular 2-应用-状态-管理-策略/](https://bertrandg.github.io/angular2-application-state-management-strategy/)

**6。Angular 生成了一个很大的包，并且构建过程很复杂。**

这种特殊的误解总是被那些试用了 Angular 2 的最初 beta 或 RC 版本的开发人员所强化。Angular 的初始版本生成了巨大的包，构建过程本身非常复杂。在早期版本中，Angular 的一个简单的 HelloWorld 应用程序将接近 1.3 MBs，这是非常荒谬的。

Angular 团队将其核心重点放在这方面，从 v4 版本开始，在这方面进行了大量优化。在这个版本之后，HelloWorld 应用包的大小已经减少到不到 **50KB** 了，这对于 Angular 开发者来说是非常棒的。Angular 提前推出了(AOT)编译器，使其能够将 Angular 应用程序编译成如此小的包大小。在使用 Angular CLI 构建时，我们可以通过使用 **-** **prod** 标志来实现这一点。AOT 也有一个高级的特性，如果和 Rollup 一起使用的话，可以让[树摇动](https://webpack.js.org/guides/tree-shaking/)，这样可以进一步减少建造规模。在这个[链接](http://blog.mgechev.com/2016/06/26/tree-shaking-angular2-production-build-rollup-javascript/)可以找到关于这个主题的很好的读物。

> **树摇动**是 Javascript 构建系统使用的一个概念，有助于在构建阶段消除死代码，从而有助于减少最终的包大小。
> 
> ## 结论

我们已经讨论了许多误解，但只关注重要的误解。Angular framework 有更多的特性，可以消除你对使用它的恐惧，至少可以帮助你尝试使用新的 Angular framework 来构建你的 web 应用。有装饰器、使用原生 CSS 动画、反应式表单模块、作用域样式、依赖注入等功能，这使得 Angular 成为一个完美的企业应用程序开发框架。它有一个非常丰富的生态系统和一个不断增长的社区，当你选择下一个 web 应用的前端框架时，这是你必须考虑的。如果你对此有任何疑问或问题，请发微博给我。

这篇博文最初发表在 Pusher 的博客-[https://blog.pusher.com/6-common-misconceptions-angular/](https://blog.pusher.com/6-common-misconceptions-angular/)上。