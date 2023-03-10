# 将脸书 SDK 与 React-Native 一起使用

> 原文：<https://dev.to/kayis/using-the-facebook-sdk-with-react-native>

这个周末，我和脸书的 React-Native SDK 争论起来，我将告诉你我的故事。

## 为什么

因此，你需要在你的应用中使用酷炫的脸书登录功能，最大限度地减少用户注册时必须输入的数据，同时最大限度地增加用户注册时提取的数据。(我不评判！)

此外，您希望在 React-Native 应用程序中完成所有这些工作，因为您对 Objective-C 或 Swift 一无所知。

## 什么

为此，脸书创建了两个 SDK。

第一个[*iOS 的脸书 SDK*](https://developers.facebook.com/docs/ios)第二个[*React-Native 的脸书 SDK*](https://github.com/facebook/react-native-fbsdk)。

最后一个基本上是第一个的包装器(还有 Android 的，我没用)

## 如何

RN SDK 只是包装了 iOS SDK，这意味着只有 RN SDK 知道 React-Native。

这意味着，你可以简单地通过运行`yarn add react-native-fbsdk`然后运行`react-native link react-native-fbsdk`来安装 RN SDK，但是它不会为你做任何事情。

iOS SDK 的安装方式就像 iOS SDK 的安装方式一样，我猜？我以前从未安装过...

为此，脸书给你一个很好的[一步一步的教程](https://developers.facebook.com/docs/ios/getting-started#settings)。除了两个问题，这已经很完整了。

-编辑-

我通过 Finder 将文件提取到`ios/Frameworks`中。

然后我打开 xCode，将它们拖到 xCode 的 Frameworks 文件夹中，并取消选择*如果需要的话复制项目*

-编辑-

首先，它想让你**而不是**选择*如果需要的话*复制项目，这对我不起作用。

第二，它要求你将文件解压到`~/Documents/FacebookSDK`，这在我看来有点奇怪。为什么不在项目目录里，像`node_modules`一样？还有，为什么要在`Documents`目录里专门注明呢？从一开始我就觉得可疑...

通过一步一步的指导，iOS SDK 开始工作，尽管我将 SDK 复制到我的项目目录中，而不是文档中。我得到了我的应用程序事件，一切都很好，但当我链接 RN SDK 的时候，事情发生了变化。

突然就再也建不起来了，因为我的`FBSDKCoreKit.h`找不到了。

当我解除了 RN SDK 的链接后，一切又恢复了正常。

那么到底发生了什么...？

`node_modules/react-native-fbsdk/ios/RCTFBSDK.xcodeproj/project.pbxproj:366`

```
FRAMEWORK_SEARCH_PATHS = (
  "~/Documents/FacebookSDK",
  "$(PROJECT_DIR)/../../../ios/Frameworks",
); 
```

**嘭！**

原来，当我链接 RN SDK 时，它会覆盖我在 xCode 中为 iOS SDK 所做的一些设置。

npm 模块深处有一个文件，它有两个硬编码的路径。

因此，似乎有人希望一步一步的指导工作，并从它添加路径，但幸运的是，他们也添加了一个相对于项目的路径。

所以，我再次从 xCode 中移除了 iOS SDK，将框架从存档中复制到我的 React-Native 项目的`ios/Frameworks`目录中，并再次添加它们。

## 结论

看起来像是一个你可以在几个小时内完成的简单任务，如果不是因为我找了 3 天才找到硬编码的路径，哈哈。

总之，学到了什么？

东西断了，一步一步走，看一步之后有没有东西断了。

另外，多读一些你使用的库的源代码，这通常比 Stackoverflow 更有帮助