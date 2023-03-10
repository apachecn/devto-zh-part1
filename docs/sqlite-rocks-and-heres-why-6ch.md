# SQLite 很棒，原因如下

> 原文：<https://dev.to/lefebvre/sqlite-rocks-and-heres-why-6ch>

以下是您应该在应用程序中使用 SQLite 的一些原因:

## 这是快

SQLite 是一个嵌入式数据库，因此数据库引擎内置在您的应用程序中。它也是为难以置信的速度而设计的。它可以轻松处理数百万行。

## 是无类型的

与其他数据库不同，SQLite 不关心您将什么类型的数据放入列中。其实它真的一点都不用类型！为列分配整数、实数、文本和 BLOB 的“关联”,这有助于确定它们如何用于查询和函数。但是您可以将文本数据放入一个整数列中，没有问题。您可以在他们的文档页面上阅读更多关于 SQLite 对数据类型的独特处理。

## 是跨平台的

SQLite 可以在任何可以想象的平台上工作，包括:Windows、macOS、Linux、Raspberry Pi、web、iOS 和 Android。此外，数据库文件本身是跨平台的。因此，如果你在 iOS 上创建了一个数据库并填充了数据，你可以将文件转移到 Windows 上，并且能够毫无困难地读、写或使用它。这使得 SQLite 非常适合像 [Xojo](https://www.xojo.com) 这样的跨平台开发工具。

## 是公共领域

因为是公共领域，SQLite 完全免费使用，没有任何限制。当然，完整的源代码很容易获得。

## 它适用于大多数编程语言

大多数编程语言都内置了对 SQLite 的支持，包括:Delphi，C，C#，C++，Go，Java，JavaScript，LiveCode，Lue，Objective-C，Perl，PHP，Python，Ruby，Visual Basic 和 [Xojo](http://www.xojo.com) 。通过 Xojo，你可以将 SQLite 用于桌面、网络、控制台和 iOS 应用。

## 它经常更新

SQLite 是一个成熟的数据库，几乎没有错误，但是它经常更新，以确保它保持最佳质量。如果有虫子溜进来，很快就被压扁了。

## 这是快

SQLite 太快了，值得再说一遍。说真的，它很快。不要使用文本文件或 JSON 或其他可能会慢很多的东西，尤其是当文件变大的时候。用 SQLite，你会更快乐。

## 它到处都在使用

您所依赖的应用程序很可能会以某种方式使用 SQLite。这是一个伟大的存储机制的偏好，一般的应用程序数据，甚至可以用作应用程序文件格式本身。例如，Apple Mail 使用 SQLite 保存电子邮件信息，Firefox 使用 SQLite 数据库存储其大部分个人资料信息。

## 免费视频

欲了解更多信息，请查看这些视频:

[https://www.youtube.com/embed/PQ36OKeIZMM](https://www.youtube.com/embed/PQ36OKeIZMM)

[https://www.youtube.com/embed/lqy9SEWRr8Y](https://www.youtube.com/embed/lqy9SEWRr8Y)

*本文原文出现在这里: [SQLite Rocks，让我们告诉你为什么](https://blog.xojo.com/2017/10/03/sqlite-rocks-let-us-tell-you-why/)T3】*