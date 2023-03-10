# Xamarin。形式动手

> 原文：<https://dev.to/stipegrbic/xamarin-forms-hands-on-35nc>

有一段时间，我想尝试 Xamarin 的多平台移动应用程序开发。最近，我得到了一个朋友建议的非常基本的应用程序想法，这看起来像是一个最终尝试 Xamarin 的完美借口。当然，微软最近收购了 Xamarin，并把这项技术免费提供给开发人员，这给了我更多这样做的理由。

### Xamarin + Visual Studio

首先，我为 Visual Studio 安装了 Xamarin 并创建了一个新项目。该解决方案中有 Android、iOS、Windows 8 商店和 Windows Phone 8.1 应用。我不能说我对没有 Windows 10 UWP 应用程序感到惊讶，即使我读到有对 Xamarin 的支持，也许现在放弃 Windows(手机)8 还为时过早。我没有读过太多的文档，而是从代码中学习。有一个共享组件，你可以把所有共享的东西，从数据库，网络调用到 XAML 写的用户界面。因为我的应用程序非常简单，而且我真的很想利用“写一次，到处跑”的口号，所以我在 XAML 写了一个分享页面。有一些常见的 Android 问题，从安装 SDK，设置模拟器，到`java.lang.OutOfMemoryError: Java heap space`错误，但最终我让它工作了。当我意识到我正在编写运行在 Android 上的 C#/XAML 代码时，感觉就像魔法一样，我无法相信这一切都行得通。但在我决定该应用程序可以在设备上测试后不久，我就把它插入并从 Visual Studio 部署了它，感觉非常自然。顺便提一下，我没有试过 iOS，因为我没有 Mac 电脑。

### 一个信号

我的应用需要推送通知服务。我已经设想过为此编写 Node.js 服务，但是在开始之前，我研究了我可以使用的现有服务。OneSignal 似乎是一个不错的选择，它提供了所需的功能，有很好的文档，许多平台的 SDK，而且是免费的。我开始使用它，并不后悔，我很快得到了推送通知。

### 结论

当然，Xamarin 的主要缺点。Forms 是您可以随意使用的 UI 控件的数量。这意味着 Xamarin。表单实际上不能用于任何带有大量 UI 定制的高级应用程序。没关系，为了这个目的，有纯 Xamarin，你可以在每个平台上本地编写 UI。但它的本意是，一种为多个平台构建简单应用程序的更快方法或原型工具 Xamarin。表单非常好用。

我最大的负面惊喜是 Xamarin.Forms 对 Windows Phone 的支持最少。我的意思是，它毕竟是 C#/XAML，人们会认为它对 Windows 效果最好，但当你仔细想想，你就会明白 Xamarin 团队没有理由付出额外的努力来支持已经可以使用 C#/XAML 开发的 Windows Phone 开发人员。他们的主要目标是 iOS 和 Android，我认为他们在实现所有这些东西方面做得很好，从 Mono、Google Play 服务、iOS 的未知功能，到 UI 层和在所有三个平台上支持 XAML。