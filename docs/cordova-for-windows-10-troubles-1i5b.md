# 科尔多瓦为 Windows 10 的麻烦

> 原文：<https://dev.to/stipegrbic/cordova-for-windows-10-troubles-1i5b>

我最近工作中的一项任务是将移动网络应用移植到 Windows 10 UWP。该应用程序是使用 Apache Cordova 构建的，由于 Cordova 和微软合作支持 Windows 10，这应该非常简单。没有人真的期望它像宣传的那样简单，我已经为多平台开发的麻烦做好了准备。

### cordova 平台添加 windows

首先，去 Cordova 网站，阅读一些关于如何添加平台和创建项目开始工作的文档。好了，添加了平台，创建了 Windows UWP 项目，我准备开始应用程序。在几天(是的，几天)解决了 html 和 web 问题之后，我终于可以运行这个应用程序了。现在这只是应用程序的 web 部分，没有应用程序需要的原生插件。有些插件是可用的，有些不支持 Windows。对于那些我们同意暂时不使用的插件，但是有一个定制的插件是我们非常需要的——用于推送通知的解析 SDK。

### 解析

我以前在原生 Windows (Phone) 8 应用程序上使用过 Parse SDK，知道如何使用 Parse。当我意识到 Cordova for Windows 10 没有使用时，第一个问题就出现了。NET C#/XAML，而是 JavaScript 应用。所以解析。这里不能使用. NET SDK。好了，Parse 还提供了其他 SDK 以及 REST API 来进行通信，所以我已经准备好适应了。我继续将 JavaScript SDK 添加到应用程序中，但是不行，它不能在客户端上用于推送通知。它只是没有所需的功能，如注册设备以接收推送通知。JavaScript SDK 主要用于服务器端和解析云代码实现。

好吧，但我知道这是一个 Windows UWP 应用程序，它应该能够执行一些。NET 代码。我继续研究如何为 Windows 10 创建一个原生的 Cordova 插件，并发现尽管它们是使用 WinJS 用 JavaScript 编写的，但也有一个选项。NET 本机插件编写为 Windows 运行时组件。在那之前，我并不熟悉这类项目，毕竟我从未真正使用过多平台技术，而是开发了原生 Windows 应用。所以这个 Windows 运行时组件有点像类库，但是限制更多，因为它可以被。网络世界。所以我创建了一个这样的项目，添加了解析。NET SDK 参考，并将其打包在一个 Cordova 插件中。应用程序正在工作！推送通知来了！让我提一下解析。NET SDK 不支持 Windows 10。但由于它最近是开源的，我对它做了一点修改，并最终使它能够工作。

### [T1。网络本地](#net-native)

我说，我们把这个打包，提交给商店。没那么快，Windows App 认证包回复。很明显，我使用了一些来自可疑的. dll 的不支持的 API。快速的谷歌搜索指出我没有使用。NET 本机编译器。这是 Windows 10 的新事物，简而言之，这意味着应用程序被编译成机器代码，并分发到商店。优点是更小的应用程序包，更快的下载和安装。好的，但是为什么它不使用。NET native，为什么需要我自己启用，在哪里启用？我对比了。由 Cordova 创建的 jsproj，和。当您从模板创建普通的 Windows 10 应用程序时，Visual Studio 会创建 csproj。本地应用有一个指令`<UseDotNetNativeToolchain>`，而我的 Cordova 应用没有。现在我不确定这是否是故意丢失的，因为 Cordova app 不是. NET 应用程序，所以可能它甚至不需要。NET 编译。但是我试了一下，哇，不支持的 API 错误消失了。我对科尔多瓦虫子的一个小胜利。

现在我有了。appx 包通过了 WACK 测试，并提交给商店。Dev Center 接着说:“你不能提交预编译的。NET 本机包。请使用。用于商店提交的 appxupload 文件。嗯，好吧，我知道。appxupload 文件是一个简单的 zip 文件。appx 文件，但是 Visual Studio 没有为我创建一个。对于我使用的 Windows 8 应用程序。商店提交的 appx 文件很好。现在我如何得到？手动加载 appxupload？在谷歌搜索了一段时间后，我发现了 MSBuild 工具，Visual Studio 内部使用它来创建应用程序包。很难找到任何关于如何使用它来生产的相关文档。appxupload 文件，但是更多的谷歌搜索让我看到了这篇[博客文章](https://oren.codes/tag/msbuild/)，它帮助我最终创建了一个. appxupload 文件。好吧，商店提交很好(不是立即，但我会跳过无聊的部分)，应用程序在商店里。安装它，试图获得推送通知，但没有，他们不会来，设备没有注册解析接收它们。似乎我的解析插件不起作用了。打开我的。appxupload 和。appx 文件中，我看到没有解析。dll 在里面。不知何故，在调试构建模式中它们是打包的，但是在发布中它们不是。这是我的 StackOverflow 问题，在我写[这篇文章](http://stackoverflow.com/questions/34722250/cordova-windows-10-native-plugin)的时候，这个问题还没有得到解答。来自 Visual Studio Cordova Tools 的团队试图回答这个问题，但无法真正帮助我解决这个问题。

### HTTP 来救援

我怀疑。dll 无法在 Windows 运行时组件和中使用。NET 本机组合，但从来没有得到任何错误或发现它在任何地方记录。所有的 Windows 运行时组件插件例子都很简单，对我的情况没有什么帮助。我试图看看当我将 Parse SDK 转换为 Windows 运行时组件时会发生什么——到处都是血腥的红色错误符号。这将是一个项目本身修复这一切没有保证这将是可能的，并在所有工作。尽管我花了很长时间试图让它在发布模式下像在调试模式下一样工作，但最终我还是采用了另一种方法——解析 REST API。官方文档声明推送通知只能由本地客户端 SDK 使用。设备推送通知注册由 SDK 完成，并在服务器端解析自己的逻辑。然而，我试图通过 REST API“强制”创建设备安装对象来模拟原生 SDK(实际上是一个 HTTP POST 请求)。没想到这么简单就成功了，但是成功了！设备注册正常，推送通知再次工作。好了，我实现了设备注册逻辑，一些我们需要的关于通道操作的方法，以及 JavaScript 中的推送通知处理，一切准备就绪。

好吧，我终于可以摆脱所有的。dll 和。净原生烦恼。我又打包了一遍，提交给商店很顺利，这一次应用程序工作正常。我甚至试图利用本地的 Cordova 命令来构建项目，但是没有成功。它完全坏了，以至于一团糟。不必要地将应用程序 Id 属性更改为无效值。这就是 Cordova for Windows 10 的可悲之处，它还没有完成，没有文档记录，还没有准备好投入生产使用。微软宣传的 Cordova Tools for Visual Studio 并不是你所期望的使用 Cordova 构建 Windows 应用程序的工具。相反，它是一个 Visual Studio 扩展，使您能够在一个 IDE 中构建基于 Cordova 的 iOS 和 Android 应用程序以及 Windows。这并不令人惊讶，但这只是又一个迹象，表明微软现在(过于)专注于构建 iOS 和 Android 应用程序，而 Windows 应用程序开发尚未完成。