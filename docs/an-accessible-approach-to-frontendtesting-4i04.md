# 前端测试的一种可行方法

> 原文：<https://dev.to/sergiodxa/an-accessible-approach-to-frontendtesting-4i04>

> 测试很难。测试前端更难。但无论如何你都应该这么做。

现实的测试通常被避免，这甚至经常发生在前端代码库中，因为测试 UI 比仅仅做单元测试或 HTTP 请求来检查 API 的响应更难(*我不是说测试后端很容易*)。

这里的问题是做测试是重要的，避免测试是不负责任的，它们允许在到达用户之前早期检测到错误。

在这里，我将列出并回顾一些测试前端代码时有用的技术，这些技术可以逐步实现。

## 棉绒

第一种最简单的“测试”代码的方法是使用代码棉条。linter 可以用来检测常见问题，它们可以是简单的打字错误，如缺少逗号、分号或括号，不良习惯，如使用变量而不是常数，或者是样式指南问题(您也可以使用[appellister](https://github.com/prettier/prettier)作为样式指南)。

linter 静态分析代码以检查它是否遵循配置的规则，它们也可以用于绑定过程，并且由于 IDE 集成，甚至可以在代码编写时和到达浏览器之前检测问题。

在 JS 世界里有很多可能的 linters， [ESLint](https://eslint.org/) 是可配置的，可扩展的，有很多特性。

但是配置工具并不有趣，所以还有其他像 [Standard](https://standardjs.com/) 或 [xo](https://github.com/sindresorhus/xo) 这样不需要任何配置的 linters。在内部，他们使用 ESLint(并且可以作为 ESLint 的配置使用),所以如果项目需要，他们可以使用 his 规则进行扩展。

## 对业务逻辑进行单元测试

当谈到自动化代码测试时，通常会想到单元测试。在前端单元测试中，代码很难，因为用简单的单元测试来测试 UI 并不那么容易(也不那么有用)。

但是前端代码库不仅仅是 UI，它通常具有独立于 UI 代码的业务逻辑。并且可以对业务逻辑进行单元测试。

在一个[React](https://reactjs.org/)[+](https://github.com/reactjs/react-redux)[Redux](http://redux.js.org/) 应用程序中提供一个例子，Redux 应用程序可以进行单元测试，类似于 [Redux Sagas](https://redux-saga.js.org/) 的东西允许更容易地对异步相关代码进行单元测试。

## 用户界面快照测试

到目前为止，我提到了 linters 来检查常见的错误和业务逻辑的单元测试，但是 UI 呢？整个前端的想法是关于用户界面。

[快照](https://medium.com/@dschmidt1992/jest-snapshot-testing-3ef9fa1222bb)测试是一种回归测试。这意味着首先要编写代码，测试它的工作情况(或者通过单元测试，或者直接运行它)，然后编写测试，将来如果代码发生变化，测试会验证一切是否仍然工作。

因为这种测试验证了代码在发生更改时仍按预期工作，所以可以用来检查用户界面的呈现。在与 UI 相关的代码中(比如 React 组件)，这意味着组件是否返回预期的结果。

> 在 React 和 React Native 中用 [Jest](https://facebook.github.io/jest/docs/en/snapshot-testing.html) 很容易做到这一点，但在 Vue.js 和 [Angular](https://github.com/synapse-wireless-labs/angular-snapshot-testing) 中也可以做到。

当然，这也可以用于非 UI 代码，为代码添加一些回归测试。

## 复杂工作流的端到端测试

复杂的工作流在许多 web 应用程序中很常见，快照或单元测试并不意味着工作流会按预期工作。端到端或 **e2e** 测试可以用来测试这个工作流。

在 e2e 测试中，浏览器打开，用户工作流自动运行。这意味着这种测试通常很慢，因为他们需要打开一个新的浏览器，加载网站并与之交互。

由于它们除了速度慢之外并不容易编写，所以开发人员最终会避免使用它们。但是这种测试在复杂和重要的工作流中更有用，比如支付工作流，因为当然每个人都希望支付工作流没有问题。

与其他测试一样，有许多可能的工具可以做到这一点， [Selenium](http://www.seleniumhq.org/) ， [TestCafe](https://devexpress.github.io/testcafe/) ， [PhantomJS](http://phantomjs.org/) ， [Headless Chrome](https://developers.google.com/web/updates/2017/04/headless-chrome) ，[puppet er](https://github.com/GoogleChrome/puppeteer)以及许多其他工具。

## 逐步实现

虽然开始一个新的应用程序很容易从测试开始，并且从头开始做所有事情，但是在大多数情况下，项目已经存在并且有许多遗留代码。那么如何实现这一切呢？循序渐进。

试图在已经存在的代码库中获得 100%的覆盖率比从零开始更难。不要用所有的测试创建一个大的拉请求，而是逐步实现它们。

从 linter 开始，是最容易使用的，在大多数情况下，它甚至不需要任何配置。

然后是单元测试，它们允许测试逻辑，通常 UI 是你可以确保它工作的东西，手动检查，但在大多数情况下，当他们需要在每次更改后手动执行用户流程时，自动测试逻辑会更快。

在单元测试从 UI 开始之后，更容易对没有逻辑的 UI 组件(纯组件)进行快照。这可以用 Jest 这样的工具来完成，如果你使用 React with[styled-components](https://www.styled-components.com/)快照可以[测试 CSS](https://github.com/styled-components/jest-styled-components) 代码(确保 CSS 总是相同的)。

复杂的工作流应该在执行其他测试时进行测试，不需要测试每个用户流，但是测试的用户流越多，部署起来就越安全。

## 最后的话

总结一下，测试很重要，如果逐步实现不会成为负担，最终整个代码都将被测试，部署也可以在每次测试后自动进行。

这可以保证应用程序的稳定性和部署的安全性，而不用担心整个应用程序会在生产中崩溃。

> ![Sergio Xalambrí profile image](img/5d19136d3aa00056b231b80de2add186.png)Sergio xalambrí[@ sergiodxa](https://dev.to/sergiodxa)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)始终测试您的前端
> -针对常见 bug 的 linter
> -针对您的逻辑的单元测试
> -针对您的 UI 的快照
> -针对复杂用户流的端到端02:17AM-2017 年 8 月 11 日![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)

[![Subscribe to learn when I publish new essays on https://sergiodxa.com/subscribe/](img/7e68c2ede6ebb6eea7da7724bdd9d783.png)T2】](https://sergiodxa.com/subscribe/)