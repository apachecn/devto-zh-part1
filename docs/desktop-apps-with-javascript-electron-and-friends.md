# 带 JavaScript 的桌面应用:电子和朋友

> 原文：<https://dev.to/kayis/desktop-apps-with-javascript-electron-and-friends>

想象一下:你会见了有史以来最资深的前端开发人员，他们告诉你 Web UIs 完全是平民阶层，你不可能比编写桌面应用程序更高级了。所以你四处搜索，发现所有这些疯狂的东西，如 GTK+、Qt 和 Swing，你意识到你甚至对 Java 或 C++一点都不了解。

那么，作为一名 JavaScript 开发人员，您该如何坚守阵地呢？

请放心，因为有项目可以拯救你的开发信誉！

## NW.js

第一个是 [NW.js](https://nwjs.io/) 。一开始它被称为 node-webkit，但我猜因为他们使用 Chromium，而 Chromium 本身使用 Blink 而不是 webkit，这个名字不再有什么意义了。

无论如何，它的想法是将 Node.js 和 Chromium 合并成一个 Web 应用程序的运行时环境，可以访问系统 API。因此，您可以使用所有的 Web 技能来创建桌面 ui，然后使用 Node.js 技能来做所有常规的系统工作，比如套接字或文件系统访问。

整个运行时间是相当沉重的，虽然。此外，内存使用不太像 Qt 应用程序；)

哦，它可以直接运行 Chrome 应用程序，如果你喜欢的话。

## 电子

电子公司比 NW.js 晚了几年，做的事情有点不同。它也使用 Node.js，但是它只使用了 Chromium 的一部分，这应该会导致更快的构建时间。

此外，虽然 NW.js 更以浏览器为中心，但 electronic 首先关注 Node.js。因此，不是从网页开始，而是从 Node.js 进程开始，并从那里创建网页窗口。

它和 NW.js 一样重量级，但是最近似乎被更多的项目使用。

## 巢

Nidium 是这个街区的新成员之一。它没有使用 Chromium 或 Nodejs，而是使用了用 C++编写的 Mozillas JavaScript 引擎 SpiderMonkey，以及一些额外的功能，如网络、数据存储和视频。

比 Electron 和 NW.js 小很多，但是不包括一个完整的浏览器。

你可以得到像 Canvas 2D 上下文和 WebGL 这样的 API。它有自己的小型布局引擎，甚至有一个 DOM compat 层，所以你可以使用 React 这样的框架来构建你的应用程序。

我猜不是所有的 Electron 和 NW.js apps 都可以用 Nidium 重写，但很可能是其中很好的一部分。

它也可以在移动设备上运行。

## 电子

电子是一个真正新鲜的事物。该项目刚刚开始作为一个概念验证，但它基于一个有趣的想法，使用操作系统的预装浏览器而不是 Chromium，并重新实现大多数电子应用程序使用的 Node.js APIs。

因为 Chromium 在 NW.js 和 Electron 中占很大一部分，这可能会导致较小的二进制文件。

## 结论

嗯，可能取决于你需要什么，就像所有的东西。:D

我想 Nidium 可能很适合玩游戏之类的。

“电子还是电离子？”(如果它变得可用的话)可能归结为一个问题“你想在多大程度上依赖操作系统？”。发布微小的二进制文件很好，但是控制所有的库也很好。