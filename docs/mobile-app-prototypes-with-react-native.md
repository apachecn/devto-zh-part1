# 带有 React-Native 的移动应用原型

> 原文：<https://dev.to/kayis/mobile-app-prototypes-with-react-native>

来自网络开发，我习惯于快速建立前端原型。几乎每台电脑都有运行 JavaScript 的浏览器，甚至大多数移动设备都有。因此，建立一个原型并在一些用户中试用并不是什么大任务，所以我担心如果我开始开发原生应用程序，这一切都会失去。

[反应——本土](https://facebook.github.io/react-native/)和[世博](https://expo.io/)来救援了！

当我第一次接到做原生应用的询问时，我四处搜索，发现了 React-Native，它允许我使用我的大部分 JavaScript 和 React 技能来编写原生应用。与一些人认为的相反，React-Native 应用程序不会在 WebView 中呈现。是的，编写 RN 感觉有点像干预 DOM 元素，特别是因为 CSS 的很大一部分也是可用的，但没有 DOM，只有 React 管理的原生视图，似乎是出于性能原因，但我从未尝试过像 [Cordova](https://cordova.apache.org/) / [PhoneGap](https://www.phonegap.com/) 这样的解决方案，所以我不知道他们的痛苦。

当编写 React-Native 应用程序时，您通过 iOS 上的 JavaScriptCore 或 Android 上的 V8 来控制其原生部分。这允许你编译一次你的原生部分，然后更新控制的*JavaScript。苹果甚至明确允许你动态更新运行在 JavaScriptCore *上的 JavaScript】，而不需要经过 app-store 的批准*，如果你只是用它来修复错误*并且有[服务允许你这么做](https://microsoft.github.io/code-push/index.html)。**

好像光是这个还不够酷，还有世博会。

Expo 的一部分是一个名为 Expo Client 的预组合 React-Native 应用程序，一些合理的默认设置和一堆通常需要你自己编译的本机模块。这款应用在 [App Store](https://itunes.apple.com/app/apple-store/id982107779) 和 [Play Store](https://play.google.com/store/apps/details?id=host.exp.exponent) 中，可以像我开头提到的无所不在的浏览器一样安装。它使用*即时* JavaScript 更新来获取并运行你的代码，*就像一个浏览器，但是是针对本地的。*

另一部分是云服务，它处理您的新代码，并使其对他们的客户端可用。所以你设置你的本地开发机器，用 JavaScript 写你的应用程序，把它推给他们的服务，然后在移动客户端上下载。*这个*不需要 Mac。

客户端已经带来了一个不错的本地模块选择，随着最新的更新，它现在甚至有更多:矢量图标，GLView，Stripe 支付，脸书和脸书广告，音频，视频，相机，文件系统，SQLite 等等。

你甚至可以在没有局域网云服务的情况下运行整个过程。只是用 [Create-React-Native-App](https://github.com/react-community/create-react-native-app) 代替。它运行一个本地服务器，Expo 客户端用它来拉你的应用程序。

Expo 和 Create-React-Native-App 的问题是，你不能使用你的*自己的*原生模块，但是你可以*把*从他们的客户端分离出来，做你自己的编译。

我已经用它开发了几个应用，*用自己的编译和使用 Expo 客户端分离了*，感觉有点像 Web。有些东西不见了，比如隐式文本节点。另一方面，它还允许许多浏览器中没有的 ES-next 特性，比如类属性或静态属性以及对象扩展和 rest 操作符。

为了帮助你开始，我在 Skillshare 上制作了一个小视频课程。

如果你需要一些帮助来开始使用 React，我还在 Github 上做了一个[免费的 React 基础课程。](https://github.com/kay-is/react-from-zero#lessons)