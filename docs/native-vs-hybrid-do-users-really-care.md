# 原生与混合:用户真的关心吗？

> 原文：<https://dev.to/antoniovdlc/native-vs-hybrid-do-users-really-care>

这一直是移动应用程序开发人员之间冲突和分歧的持续来源，几乎超过了标签对空间的永恒核战争(嘿，这可以成为下一篇文章的一个伟大主题！).但首先，我们来看看什么是原生和混合移动应用。

# 原生和混合移动 app 有什么区别？

原生应用和混合应用的主要区别在于构建它们所使用的技术。原生应用是使用平台的原生 SDK(软件开发工具包)开发的，开发者可以在 Android 的 Java 中使用，或者在 iOS 的 Objective-C 中使用。另一方面，混合应用程序是使用 web 技术(HTML、CSS、JavaScript)开发的，然后要么被编译成本机代码，要么被打包成本机 web 视图。

大多数其他差异都是技术差异的结果。混合应用大多通过在 SDK 上增加一个抽象层次来鼓励平台间的代码重用。因此，你可以编写一个应用程序，并让它在 Android，iOS，Windows Phone，FirefoxOS 等平台上工作。另一方面，如果你想构建原生应用程序，你需要为每个平台构建完全不同的应用程序。因此，与混合应用相比，原生应用的构建和维护成本更高。但从好的一面来看，原生应用比混合应用拥有更高的性能，而且它们享受着轻松的原生用户界面。

# 脸书和领英，从混血儿到原生

成为原生或混合一直是一个相当困难的选择。一个原生 app 的成本值得吗？混合应用的性能有那么差吗？要回答这些问题，我们可以看看脸书和 LinkedIn，它们都是在转向原生应用之前开始发布混合应用的。

脸书首席执行官马克·扎克伯格(Mark Zuckerberg)有一句名言:在 HTML5 上下太多赌注是脸书的[【最大的错误】](http://techcrunch.com/2012/09/11/mark-zuckerberg-our-biggest-mistake-with-mobile-was-betting-too-much-on-html5/)，数字证明了这一点。脸书很难通过混合应用程序为其用户提供良好的移动体验，在切换到 iOS 和 Android 上的原生应用程序后，用户满意度飙升！LinkedIn 的故事略有不同，更多地关注于[当时混合应用缺乏可用的开发工具](http://venturebeat.com/2013/04/17/linkedin-mobile-web-breakup/)，但他们可能也受益于整体性能更好的应用。

事实上，几项研究显示了用户对应用程序性能的关心程度。56%的用户在移动应用上遇到过性能问题，而 [79%的用户可能会再试一次](http://www.ymedialabs.com/hybrid-vs-native-mobile-apps-the-answer-is-clear/)，超过 [33%的用户最终会转向竞争对手](http://www.ymedialabs.com/hybrid-vs-native-mobile-apps-the-answer-is-clear/)。此外， [84%的用户认为性能在移动应用中有些或非常重要](http://www.ymedialabs.com/hybrid-vs-native-mobile-apps-the-answer-is-clear/)。

# 那些 app 是混合的吗？(你真的在乎吗？)

因此，用户确实关心性能，而众所周知，混合应用在这方面落后于本地应用。但这是否意味着用户关心使用原生还是混合应用？这里有一个小游戏:寻找混合应用程序！

[![Instagram](img/917a8719f67cd1d78a760f2fba27009e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--58K6gFMR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AWHvLvZTqq9vKq8x3.png)
[![Wikipedia](img/0f10c6e74518eed535227b50db1d5aa2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6DmO0c4u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2ASd4uA7g6w1JCSXrw.png)
[![Twitter](img/18953c49e0454a0714f790aa44a57436.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BdkmEU1N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AgwbV0vgrjTnGIAjl.png)

回答:都是！

# 我们签个停火协议，把事情搞定！

据加纳称，到 2016 年，超过 50%的移动应用将是混合应用。开发者也倾向于开发混合应用，33%的人倾向于开发混合应用，相比之下，29%的人倾向于开发原生应用。

此外，新工具如[反应原生](https://facebook.github.io/react-native/)(由…脸书建造！)正在弥合原生和混合之间的差距，[允许开发者使用 web 技术构建接近原生的性能应用](https://medium.com/ios-os-x-development/an-ios-developer-on-react-native-1f24786c29f0)。使用混合技术很容易构建一个性能很差的应用程序，但是使用同样的技术也有可能构建出优秀的应用程序。

最终，[用户并不太关心一个应用程序是如何开发的](https://blog.nraboy.com/2015/02/ive-gone-hybrid-native-mobile-apps/)，但他们确实关心这个应用程序实际上做了什么，以及它的响应能力如何，所以[让我们签署一份停火协议，把事情做好](http://readwrite.com/2015/02/27/native-vs-web-apps-ceasefire)！