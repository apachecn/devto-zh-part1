# 反应和角度之间的错误比较

> 原文：<https://dev.to/swiip/wrong-comparisons-between-react-and-angular-2md6>

[![](img/33a4b2d3c57432f611e4747dd4e6cd4d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NMwjy1T2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AJ1dPk8j0_2RAlzAT7aGx1A.jpeg)

一段时间以来，我已经习惯了在 Angular 1，Angular 2 之间切换，并在不同的项目中做出反应。我也和许多不同的人交谈过，他们都是这个或那个框架的粉丝。现在，我能够比较每个解决方案的真实情况。

这篇文章的目的不是为了定义哪一个是最好的而在这些框架之间做另一个比较。而是指出我听到的最常见的比较论点是错误的。

这篇文章的大部分不会区分角度 1 和 2，因为这两个点都是有效的。Angular 1 和 Angular 2 之间的相似之处比人们倾向于同意的要多，但这是另一篇文章的主题！

### Angular 是 Fullstack，React 是一个微型 UI 库

那有点开玩笑的意思，抱歉用词激烈。通过考虑这两种解决方案的初始设计，也许是的。但是今天 React 是一个完整的平台。最近，有谁将 React 作为他们更大的 Web 应用程序的一个小块砖呢？很多时候，UI 框架是你前端 app 的首选:你会选择 Angular 还是 React(或者别的什么)？

一旦你选择了 React，你几乎被迫使用 Babel 和 Webpack，现在你会使用 create-react-app，你会使用 react-router，因为它几乎是唯一的解决方案。您的代码库肯定会完全由 React 的选择决定。所以基本上它有一个“框架”的角色，而不是一个微小的库，不管你想不想要。关于 React 中你的应用程序的逻辑结构，我建议你等待下面的第二部分。

另一方面，没有人问 Angular 的问题，因为它是作为一个全功能框架出售的。顺便提一下，Angular 2 通过不同的项目( [Angular Universal](https://universal.angular.io/) )支持服务器端渲染，而 React 支持开箱即用。因此，两个框架的定位肯定有点不同，但从更大的角度来看，它们都是“大框架”。

为这种差异辩护的人往往会带来一个奇怪的最后论点。是“请求者库”(你知道的，fetch，$http，http，axios，superagent)的问题。好吧，React 不提供，Angular 提供。这几乎是 React 唯一真正的自由，所以我认为这不足以在两种解决方案之间保持这种牢固的界限…

### Angular 组织你的逻辑，React 不行，你得用 Flux

如果你用 Angular 或者 React 看大部分开源项目，确实如此。大多数 Angular 应用程序都是用“Angular services”来组织的，大多数 React 应用程序都使用一个 [Flux](https://facebook.github.io/flux/) 实现。然而，我最近花了一些时间来真正分析 React 和 Angular 在这个问题上的概念差异。

我的结果与普遍公认的大相径庭。角度不是组织你的逻辑。Angular 为您提供了依赖注入系统，它允许您轻松地管理 singleton 并提高可测试性。但是你的逻辑仍然包含在一大堆单态中。开发过大型 Angular 应用程序的人都知道，单例对于逻辑组织来说是不够的。

这种单例方法在 React 和 JavaScript 模块中完全可行。你定义一个类，然后导出它的一个实例……瞧，或者你可以只导出一个对象文字。你完全“可以”用这个来开发相当大的 React 接口。

但是在现实世界中，在脸书意图的推动下，反应“社区”开始认为这还不够，需要更多。他们添加了通量模式，现在，(几乎)每个人都同意使用 [Redux](http://redux.js.org/) 。但 Redux 与 Angular services 不在同一水平上，它是一个新的范式，我已经看到许多 Angular 应用程序也可以/将/受益于这一新范式。

我的观点是:Angular app 可以拥有完全基于依赖注入单例的架构。同样，React 应用程序也可以由 JavaScript 模块 singletons 专门管理。Flux 和 Redux 是前沿模式，对任何应用程序都很有趣，不管它使用什么框架。

### Zone.js 是变化检测的革命

我不是一个真正的专家，不知道 [Zone.js](https://github.com/angular/zone.js) 是如何工作的，但它的主要特性是对所有浏览器事件进行猴子式修补(替换内置函数),让一个框架，在这个例子中是 Angular 2，知道是否发生了任何需要新渲染的事情。

要触发渲染，在 Angular 1 中，必须调用$scope。$apply()，一般来说，框架会为你做这件事，你不必自己去做，但基本上它是一种机制。在 Angular 2 中，有了 Zone.js，您完全不必担心:Zone 会在没有任何显式调用的情况下监视是否有内容追加。反应呢？React API 通过让你改变道具或者调用 setState 让事情变得简单了很多，所以基本上，它是完全显式的。如果你不同意，就不要重新渲染。

所以我的观点是，你可以喜欢或不喜欢 Zone.js。但你必须明白，这不是变化检测魔法发生的地方。这是触发变更检测的一个便利条件，除此之外别无其他。

最后，回到 Redux(或 [NgRxStore](https://github.com/ngrx/store) )，如果您使用这样的解决方案，无论您使用什么框架，Redux 都会意识到应用程序状态中发生的任何变化，并能够触发渲染。在这种情况下，也不需要 setState、$apply …和 Zone.js。

### 那么……有什么区别吗？

是的，当然有。只是不是这些！这几天我最看重的是全局设计和 API。API 是你大部分时间要处理的东西，你最好喜欢它。我正要用 2 或 3 个主要想法来总结 React 和 Angular，但我意识到这太冒险了。看看每个框架的关键特性，选择最适合你的一个。

* * *