# 我从 Airbnb 开源了 3 个模块

> 原文：<https://dev.to/kristof0425/i-open-sourced-3-modules-fromairbnb>

### 背景

当我在读亚当·尼瑞(Adam Neary)的故事(T2 重组 Airbnb 的前端(T3)时，我发现了两个 React 组件，这真的引起了我这个新手前端开发者的兴趣。

给它们起名字，叫做[`<HideAt/>`](https://github.com/kristof0425/react-hide-at)[`<ShowAt/>`](https://github.com/kristof0425/react-show-at)。我对它们如此感兴趣有几个原因:

*   我想创建我的第一个 NPM 模块，但我不想做，只是因为有一个。我一直想通过创造社区来赋予社区价值。
*   [`<HideAt/>`](https://github.com/kristof0425/react-hide-at)[`<ShowAt/>`](https://github.com/kristof0425/react-show-at)，他们解决了一个问题，我甚至觉得他们身上还有更多潜力。
*   它们似乎很容易复制，尽管乍一看，我不知道应该如何开始实现它们。

出自[亚当](https://medium.com/@AdamRNeary)、 [`<HideAt/>`](https://github.com/kristof0425/react-hide-at) 和 [`<ShowAt/>`](https://github.com/kristof0425/react-show-at) 的话是:

> 它们是响应式的实用程序，允许我们在不同的屏幕尺寸下戏剧性地改变用户体验，而不必使用 CSS 隐藏和显示。这导致页面更加精简。

### 履行

首先，我去了 airbnb.com 的[，打开 React 开发工具，搜索](https://airbnb.com) [`<HideAt/>`](https://github.com/kristof0425/react-hide-at) 或 [`<ShowAt/>`](https://github.com/kristof0425/react-show-at) ，我现在记不清了。🙃很快我意识到，我必须实现 3 个模块而不是 2 个，因为它们被包装成一个名为 [`withBreakpoints`](https://github.com/kristof0425/react-with-breakpoints) 的特设模块。
( **有趣的事实**:当我第一次遇到他们的时候，我甚至不知道到底什么是 HOC。)
之后，我尽我所知，在谷歌的帮助下，试图从 Airbnb 的网站上复制 3 个模块的行为。

这三个模块教会了我一些东西:

*   阅读文档总是最短的路径
*   在你准备创建一个新的 npm 模块之前，寻找市场上的竞争对手是有用的，**不要重新发明轮子**
*   阅读竞争对手模块的源代码(如果有的话)是值得的
*   不要在如何上犹豫太多？开始做吧，最终你会成功的
*   **使用 npms.io** 看看他们是如何给你的模块排名的，这真的可以**为你**提供一些**有用的见解**

从技术方面来说:

*   什么是“s ”,为什么它们有用
*   配置`babel`和`eslint`
*   用`jest`和`Node`的`assert`熟悉测试写作
*   使用覆盖率和 Travis CI

最后，我准备好了所有的 3 个组件。我可以发布它们，但我仍然没有为它们编写任何单元测试，也没有为回购协议设置持续集成服务。

由于我每天都在使用相当多的 npm 模块，所以我倾向于那些已经编写并拥有测试的模块💯覆盖率百分比或者更确切地说是`90% <`。总之，我有工作要做，有艰苦的工作要做……至少我是这样认为的。

**特拉维斯** :
设置特拉维斯 CI ( *我选择使用的集成服务*)对我来说是一个**惊喜**。我只需要用下面的代码片段创建一个`.travis.yml`:

```
# .travis.yml
language: node_js
node_js:
  - 6
  - 7
notifications:
  email:
    on_success: never 
```

> **提示**:属性`notifications`是可选的，但是通过声明它，您可以[从 Travis 配置您的通知](https://docs.travis-ci.com/user/notifications/)。在上面的代码片段中，你可以看到我更喜欢只收到关于我失败构建的电子邮件。

**单元测试**:

看文件，看文件，看文件。😃

没有，但是说真的，我之前没有写过任何单元测试，我甚至不知道`jest`和`enzyme`的具体区别。所以你可以想象我是如何开始这个挑战的。😱

在我仔细阅读文件后，我突然意识到这也是一个惊喜。你可以很快学会编写单元测试——当然我在这里说的是一些基本的东西，比如，“组件能够接收和改变它的`props`”，但是我的信息只是试图表明**测试编写完全不难学**。😊

综上所述，这两个是我认为实施过程中最大的痛点。我只想告诉你，我错了。

### 更多潜力

在我看来 [`<HideAt/>`](https://github.com/kristof0425/react-hide-at) 和 [`<ShowAt/>`](https://github.com/kristof0425/react-show-at) 比仅仅是响应性的工具更有潜力。结合 [`withBreakpoints`](https://github.com/kristof0425/react-with-breakpoints) 和[詹姆斯凯尔](https://medium.com/@thejameskyle)的 [`react-loadable`](https://github.com/thejameskyle/react-loadable) 他们可以通过只导入客户端需要的捆绑包来提供真正精简的网页。
例如，当您在移动设备上打开页面时，平板电脑视图捆绑包和 PC 视图捆绑包可能会被忽略，因为它们在该上下文中是不必要/多余的。只有当`resize`发生时，这些包才能异步导入。

### 最后的话

如果您对这些组件感兴趣，您可以通过链接找到它们，如果您在那里，请随意提出问题或发表意见。🙂

最后但同样重要的是，我要感谢[塔玛斯·福多尔](https://twitter.com/ruffle1986)在这些项目上对我的指导，[雅各布·尤斯扎克](https://medium.com/@apertureless)关于如何发布 npm 模块的媒体帖子，以及[亚当·奈里](https://medium.com/@AdamRNeary)创建**那个**媒体帖子并共享这些组件，现在它们已被整个 React 社区共享。😉

如果你喜欢我的前 3 个 npm 模块的小故事，请点击那个小❤️图标，感谢你阅读这篇文章！