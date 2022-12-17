# 如何学习 React Native -针对 Android/iOS 开发人员

> 原文：<https://dev.to/srijith_un/how-to-learn-react-native---for-androidios-devs-2mj>

React Native 是目前构建移动应用的热门话题。它有助于用几乎相同的代码库为 iOS 和 Android 构建应用程序。由于我自己是一名 Android 开发人员，我想学习 rn，但没有 Javascript 方面的经验。于是我搜索从哪里入手，来了一些有用的资源。这就是我学习 RN 的方法。

由于我不喜欢学习视频课程，所以我主要关注提供内置编辑器和博客的教程。

要学习 React Native，您必须了解 ReactJs，因为 React Native 基于 React js——一个用于构建前端的 web 框架。因为 React 是基于 Javascript 的，所以你必须学习 Javascript。

因此，首先你必须了解 Javascript 的基础知识。

### Javascript

我发现 [Codecademy 的 Javascript 课程](https://www.codecademy.com/learn/introduction-to-javascript)对学习 Javascript 基础很有用。
然后了解一下 ES6 的特性。ES6 或 ES2015 是 Javascript 标准，提供了 Javascript 的新特性。学习 ES6 使得编写 React 和 React 原生代码变得容易得多。
你应该知道一些常用的 ES6 特性，比如**箭头函数**、**类**、**析构**、 **const** 和 **let** 关键字、 **Promise** 、**模板字符串**、**模块导入**、 **rest 和 spread 运算符**。你可以在这里了解它们

### 反应

一旦你理解了它们，就开始学习[反应](https://reactjs.org/)。你可以从官方网站开始，因为它有很好的记录，并有一个教程来建立一个应用程序。在这里使用您的 ES6 特性来适应它。

**完成本教程后，您应该已经学会的主要内容是 React 组件的[生命周期以及什么是](https://reactjs.org/docs/react-component.html#the-component-lifecycle)[道具](https://reactjs.org/docs/components-and-props.html#props-are-read-only)和[状态](https://reactjs.org/docs/state-and-lifecycle.html)**

### Flexbox

React Native 使用 [Flexbox](http://facebook.github.io/react-native/docs/flexbox.html) 布局样式，以一种更加简单和响应迅速的方式呈现视图。我觉得理解 Flexbox 的概念有点困难，但是我推荐阅读[这本指南](https://medium.freecodecamp.org/understanding-flexbox-everything-you-need-to-know-b4013d4dc9af)来了解它的全部内容。你可以在一个单独的浏览器标签中打开它，这样当你遇到任何困难的时候，你就可以去查阅它。

如果你清楚地理解了以上所有的概念，那么学习 React Native 就很容易了。

也可以选择自己喜欢的编辑器来编码。我个人比较喜欢 [Visual Studio 代码](https://code.visualstudio.com/)。它有一些很棒的特性和大量的扩展，使其易于编码。

现在你已经准备好学习 React Native 了。去他们的官方网站，浏览他们的[入门指南](http://facebook.github.io/react-native/docs/getting-started.html)来构建一个示例应用程序。阅读文档中提供的所有基础知识。

一旦你有了如何构建一个应用程序的想法，开始做一个演示应用程序来应用你所学的所有功能。我从一个 Todo 应用开始。一开始避免使用任何库。试着用你到目前为止学到的知识来构建一个基本的应用程序，或者至少完成几个屏幕。一旦你完成了，就要学会使用图书馆。

在选择图书馆时，一定要选择那些维护良好并被官方文件推荐的图书馆。

由于移动应用程序中的一个重要功能是如何在屏幕之间导航，因此了解它在 React Native 中是如何实现的很重要。在撰写本文时，RN 建议使用像 [ReactNavigation](https://reactnavigation.org/) 和 [React Native Navigation](https://github.com/wix/react-native-navigation) 这样的库，以便在 Android 和 iOS 上使用相同的代码。

浏览他们的文档，并在您的演示应用程序中应用该库。最后，学习如何构建应用程序的发布版本。

基础都学完了，再学一些高级库。例如，像 [Redux](http://redux.js.org/) 或 [MobX](https://mobx.js.org/) 这样的状态管理库。它们提供了管理应用程序状态的更好方法，以及测试应用程序的更好方法。

就是这样。如果你已经到了这个阶段，那么你已经准备好构建一个生产就绪的应用了。

如果你是一名 iOS/Android 开发者，并且学习了 React Native，请在下面的评论中分享你是如何学习 React Native 的。