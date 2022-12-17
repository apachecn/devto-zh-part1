# React 生态系统的 React 初学者路线图

> 原文：<https://dev.to/a_reiterer/a--react-beginners-roadmap-through-the-react-eco-system-2f9>

React 初学者通常很难学习 React，因为 React 的生态系统似乎很庞大。你会不可避免地很早就遇到 Redux、Webpack 或 Babel 之类的东西。在这一点上，我是如此不知所措，因为我不知道从哪里开始。

更令人困惑的是，我甚至不知道这些工具试图解决的问题。 *今天我知道得更清楚了，我要告诉你们，这些都没有必要——至少现在还没有。*

*这篇文章首先出现在[我的博客](http://www.andreasreiterer.at/web-development/react-beginners-roadmap/)上。*

## 简介

我为每个刚刚起步的人写这篇文章——**React 初学者，或者 web 开发新手**。但是，我假设您至少对 HTML、CSS 和 JavaScript 有一些基本的了解。

这篇文章**旨在成为一张路线图**，一步一步地引导你从入门级到更复杂的主题——它帮助你专注于重要的事情——而不是浪费时间担心你还不需要的东西。路线图**不会包含所有不同主题的详细解释**。深入的内容将在未来的帖子中涵盖，关于所有这些的额外详细内容将是我新项目的一部分:**[React for Noobs](https://www.reactfornoobs.com)**——React 初学者的无混淆指南。

但是够了，接下来的部分将告诉你，在 React 和它的无痛苦生态系统中，应该关注什么来提高你的知识。

## 先决条件

在开始使用 React 之前，你至少应该对 HTML、CSS 和 JavaScript 有一个基本的了解。也就是说，看看 ecmascript 6(ES6)——当使用 react 时，您至少需要

*   [箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
*   [类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
*   [模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
*   [让](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
*   [常数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)

这应该足以从基础开始。

## React 初学者基础

在开始使用 React 时，我强烈建议你从[官方教程](https://reactjs.org/tutorial/tutorial.html)开始。您将学到理解 React 最基本知识所需的一切。

在接下来的部分中，我将对你在上述教程中看到的一些东西提供一些额外的想法。

### 创建-反应-应用

这个令人敬畏的工具使得引导一个新的应用程序变得更加容易。它可以让你避免在可能会让你非常困惑的配置中瞎折腾。所以现在，坚持使用 create-react-app，很高兴只用一个命令就能引导一个新的应用程序。

### 组件

在 React 中，一切都是由组件构成的。所以，一旦你知道如何使用 JSX，让自己习惯于将数据传入组件: ***道具*** ，管理组件内部的 ***状态*** ，以及将 *状态* 提升到父组件。学习如何正确地**处理事件**或传递功能作为道具(参见[“提升状态”](https://reactjs.org/tutorial/tutorial.html#lifting-state-up))。也别忘了一点点**造型**不会有坏处:)

接下来就是学习如何做 **[条件渲染](https://reactjs.org/docs/conditional-rendering.html)** 以及如何渲染 [**列表**](https://reactjs.org/docs/lists-and-keys.html) 和 [**表单**](https://reactjs.org/docs/forms.html) ，以及如何 **[组合你的组件](https://reactjs.org/docs/composition-vs-inheritance.html)** 。

如果你学会了以上所有内容，你应该自己创建一个小应用程序来巩固你所学的内容。小而简单的东西。当时，我刚刚构建了一个 Tweet 组件，看起来应该与您在 Twitter 上看到的一模一样。你所要做的就是创建必要的组件，放入一些静态数据并对其进行处理。你也可以把它扩展成一个 tweets 列表。

如果你觉得足够舒服，你可以专注于更高级的东西。

## 高级反应

既然您已经能够创建有用的组件，那么是时候看看 **[属性](https://www.andreasreiterer.at/web-development/react-proptypes/)** 了。此外，由于您可能希望从远程服务器或 API 获取数据，请熟悉 **[生命周期方法](https://www.andreasreiterer.at/web-development/reactjs-lifecycle-methods/)** 以及如何 **[从服务器](https://daveceddia.com/ajax-requests-in-react/)** 获取数据。注意:您可以对任何 REST 后端/ API 使用 React，没有任何限制。

你可能想把你的应用分成不同的屏幕。为了处理转换，你需要 **[反应路由器](https://medium.com/@pshrmn/a-simple-react-router-v4-tutorial-7f23ff27adf)** 。替代解决方案是存在的，但是，我建议从 react 路由器开始，它似乎是社区中的首选解决方案。

如果你还没有的话，也看看如何处理**、**表单，尤其是看看 **[不受控制的组件](https://reactjs.org/docs/uncontrolled-components.html)、**，这些组件在处理表单时会用到很多次。

提示:尽可能使用功能组件 **[而不是类组件](https://www.andreasreiterer.at/web-development/react-functional-components/)** 。这也意味着，您应该尽可能地让更多的组件保持无状态。当您没有将状态分散到整个应用程序中时，您会很感激的——特别是当您必须进行一些重构时。为了进一步阅读，我推荐丹·阿布拉莫夫关于 **[表示和容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)的文章。**

**[错误界限](https://reactjs.org/docs/error-boundaries.html)** 用 React 16 引入。它们捕捉 JavaScript 错误，避免整个应用程序崩溃。相反，React 将显示一个后备 UI，而不是失败的子组件树。

为了首先避免错误，看看**[Jest](https://facebook.github.io/jest/docs/en/getting-started.html)**—所有用 create-react-app 创建的应用都支持 Jest 测试。Jest 很酷的一点是，你可以测试 React 组件树的快照。这使得创建测试更加容易。

## 现在怎么办？

现在你知道很多关于反应。您应该能够创建一些与后端服务器或 API 通信的复杂应用程序。

在某一点上，你可能会意识到，你的应用程序越复杂，管理状态就越困难。您现在应该已经看到了使用 Redux 或 MobX 进行的**状态管理。**我建议你两样都试试，然后决定哪一个最合适。许多 React 初学者在 React 职业生涯的早期就发现了 Redux，然后试图将它应用到所有的应用程序中。大多数情况下，这只会增加复杂性，而不是消除复杂性。

在某些情况下，您希望在服务器上呈现 React 应用程序。出于 SEO 或性能原因，这可能是必要的。我把这个放在这里，因为我真的相信 React 初学者不应该考虑服务器端渲染。

如果你想在构建过程中或者在隐藏的配置文件中改变一些东西(多亏了 create-react-app)，你可以 [**弹出**](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#npm-run-eject) 它。我不建议 React 初学者这么做，因为这会立即增加应用程序的复杂性。所以，通过熟悉**网络包**和**巴别塔**来做好准备吧。他们的配置文件会放在你的应用程序文件夹中，你应该知道他们做什么。

## 包装完毕

React 初学者往往很难进入其中。一旦你掌握了基本知识，知道如何一步一步地进行，事情就会变得容易得多。我希望这个指南能帮助你理解作为一个初学者，你真正需要什么，以及你应该推迟到以后做什么。

我想让 React 初学者的生活轻松一点。这就是为什么我最近开始为 Noobs 开发 React。这是一个循序渐进的指南，牵着 React 初学者的手，一步一步地指导他们从 Noob 到 Pro。

[![React for Noobs - A Guide from Noob to Pro](img/b57ec99aad1ec834b682803810766fd9.png)T2】](https://www.reactfornoobs.com)

[React for Noobs -从 Noob 到 Pro 的指南](https://www.reactfornoobs.com)

## 还有更多...

您想了解更多关于 React 及其生态系统的信息吗？好消息！订阅我的[时事通讯](https://www.andreasreiterer.at/weekly-webdev-newsletter/)，让更多的文章免费发送到你的收件箱！

* * *

感谢阅读文章直到最后！如果你让我知道你的想法，我将不胜感激。请发表评论，在 Twitter 上联系我，或者发邮件给 T2，你好，我很乐意回复我收到的任何信息。