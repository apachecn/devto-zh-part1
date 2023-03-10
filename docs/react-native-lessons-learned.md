# 本土反应:经验教训

> 原文：<https://dev.to/kayis/react-native-lessons-learned>

这个月我开始了一个手机应用项目。为什么？自 2006 年以来，我一直是一名 Web 开发人员，并像躲避瘟疫一样避免卷入整个“原生应用程序开发”的事情。我在 2007 年至 2011 年上大学，那时 iPhones 和 Android 刚刚问世，我的大多数同学在学习期间或毕业后都进入了移动领域。

他们赚了很多钱，但对我来说这听起来很可怕！

目标-C？谁用这个？！

Java？这是我学习的祸根！

此外，赚大钱的似乎只有 iOS 应用程序，我从心底里讨厌苹果。

那么，我是怎么陷入这种困境的呢？

我确实在 React 工作了两年，并认为用 [React Native](https://facebook.github.io/react-native/) 做应用程序不会有太大的不同，所以我得到了一个 RN 项目，现在我已经在这个项目中工作了一个月。现在我要告诉你我学到了什么。

## 无需 Mac 即可反应原生 iOS 应用

这是可能的。例如，有一个名为 [Expo](https://expo.io/) 的项目，它可以让你在他们的服务器上进行构建，你只需要在你的应用程序中编写 JavaScript。他们给你一个客户端，基本上是一个 RN 运行时。它在 play store 和 app store 中，所以你只需给自己一部 iPhone，安装 Expo 客户端，扫描二维码并运行你的应用程序。

这个过程的一个精简版本以 [create-react-native-app](https://github.com/react-community/create-react-native-app) 的形式存在，它基本上建立在 Expo 之上，但不需要 Expo 帐户就可以开始。

此外，Expo 允许您通过代理在互联网上运行其客户端应用程序，这样，CRNA 只能在您的本地网络中工作。

有什么条件？总有一个陷阱！

你只能使用 RN 和 Expo 中包含的原生模块，但至少 Expo 给你带来了一堆有用的额外组件，如视频、音频、通知和 GL，所以可能 80%的应用程序都会与此相处。你可以安装所有你喜欢的 JavaScript 模块，只是不能安装原生的。

## React Native 做什么和不做什么

当我开始整个事情的时候，我认为 RN 将是一个完整的开发体验，但是，我错了。RN 帮助你开发并使你能够使用你的 JavaScript 和 React 技能来开发本地应用，但它不会帮助你在应用商店中获得你的应用。它甚至不能帮助你在所有测试者的设备上安装它。

如果你使用 CRNA 或 Expo，你可以在任何 iPhone 上的 Expo 客户端运行你的应用程序。Expo 甚至给了你一个构建服务，你可以进入应用程序商店，但是如果你不想依赖 Expo 服务或者你需要 Expo 没有提供的其他本地模块，你可能需要自己构建你的应用程序。

如果你在 Mac 上用普通的 RN 进行开发，你可以在模拟器或者 USB 连接的设备上运行你的应用，但是 RN 会在你的应用只是一个普通的 Xcode 项目的边界上停下来。

因此，您已经完成了开发，现在您想要构建您的 IPA，以便将其交付给同事的测试设备。

React Native 不会签署您的代码、打包您的应用程序或为您创建预置描述文件。这些都是苹果概念，与 RN 无关。

但幸运的是，有其他工具可以帮你做到这一点。

我找到了[浪子](https://fastlane.tools/)，它会做所有繁重的工作。它有非常好的错误信息，涵盖了你在编写应用程序并在*你自己的*设备上测试之后*需要做的所有事情。它甚至似乎知道 RN，并给出一些关于在哪里运行`fastlane init`或`fastlane match init`的提示，最好的是，它完全在 shell 中运行，因此您可以将它很好地集成到您的`package.json`中，运行`npm run build:ios:beta`并完成它。不用再研究 Xcode 了。*

## 创建 React 原生应用的问题

我用 CRNA 开始，它有我需要的大部分功能，而且设置起来真的很容易。5 分钟后，我可以用 JavaScript 编写我的业务逻辑，一切都很好。

为了获得正确签名的 IPA，我需要使用 Expo build 服务，这需要一个 Expo 帐户。如果你不想这样，你需要将 CRNA 项目中的 T1 弹出到一个普通的 RN 项目中，然后在 Mac 上自己构建。

弹出比我想象的要难一点，尤其是如果你想自己动手做的话。

有两种选择，要么是 [ExpoKit](https://docs.expo.io/versions/v16.0.0/guides/detach.html) 要么是常规 RN 弹射。

ExpoKit 选项仍然要求您使用 Expo 服务进行构建，因此它不是一个选项。

RN 不允许我保留我正在使用的 Expo 模块。这还不算太糟，因为其中一些包只是常规原生 RN 模块的包装，我找到了其他所有包的替代品。唯一咬我的是`vector-icons`。我到处使用它们，但 CRNA 没有告诉我它们不再有效。所以弹射后一切都爆炸了。我很快就修好了，Expo 模块只是一个包装器，我可以使用常规的`react-native-vector-icons`模块。

此外，似乎弹出没有正确设置`.plist`文件中的所有值。我有一个`app.ios.js`,它注册了一个与我的应用程序不同的名字，所以 Xcode 找不到它，但我发现 Xcode 错误足以修复这些问题。

## 什么时候用什么？

当我这样做的时候，我有一种感觉，这整个反应原生的东西有不同的层次，根据你的需要，你不必去*全进*。

### 原型制作

[创建 React 原生 App](https://github.com/react-community/create-react-native-app) 大概就够了。您将获得所有 React Native 和 Expo 组件和 API，并将创建一个应用程序，您可以非常快速地向您的同事或客户展示。

你甚至可以*弹出*并使用你的大部分代码。如果你不想使用 ExpoKit，你只需要用不同的东西替换 Expo 组件和 API，这需要使用 Expo build 服务。

### 没有 Mac 可供开发

[Expo](https://expo.io/) 是一条必经之路，你可以获得 CRNA 的所有功能，以及包装你的作品的构建服务。它需要一个(免费的)世博会账户。此外，您只能使用包含的本地 API 和组件。

你可以*分离*，这是 CRNAs *弹出*的 Expo 版本，从那时开始做常规的 RN 开发。

### 需要其他原生 API 和/或组件

你可以在`react-native init`之后使用 CRNA 和*弹出*，这样你就可以得到一个很好的项目结构，并以一个普通的 React 原生项目结束。然后，您可以使用自己喜欢的本机模块。

对于大多数原生 API(可能是全部？)已经有了所谓的 React Native bridges，你可以通过 npm 安装，并通过[链接](https://facebook.github.io/react-native/docs/linking-libraries-ios.html)包含在你的项目中，所以你不必自己编写 Objective-C 或 Swift。

你将需要设置你自己的构建工具 tho，但是[浪子](https://fastlane.tools/)真的很好，可以帮助你完成所有的步骤。