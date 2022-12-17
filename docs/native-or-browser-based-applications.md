# 本地应用还是基于浏览器的应用？

> 原文：<https://dev.to/martin/native-or-browser-based-applications>

今天使用的许多应用程序都是基于浏览器的技术，如 electronic。Spotify 从原生应用程序过渡到基于浏览器的应用程序。Spotify 这样做是基于微服务文化。

我今天一直想知道这种类型的软件的需求。我们更喜欢基于浏览器的桌面应用程序，而不是本地应用程序吗？如果我们以原子和崇高文本为例。Atom 是一个电子应用程序，其中 Sublime Text 是原生的。

当在 Atom 中打开大文件时，你真的会发现它有多慢。是的，看起来他们正在提高速度。但是 Sublime Text 确实读大文件快一点。

关于电子应用程序相对于本地应用程序的大小，我只是克隆了[电子样板](https://github.com/szwacz/electron-boilerplate)库。然后我用[电子打包器](https://github.com/electron-userland/electron-packager)将它构建成一个可执行文件。那东西 125MB。对于一个 Hello World 应用程序来说，这是一个巨大的数字。

到目前为止，在我看来，native 在性能和尺寸方面更胜一筹。再来看看其他几点。

由于电子是一个基于浏览器的堆栈，我们可以在网络编程语言中工作。这是一个优点，许多 web 开发人员现在也可以在桌面应用程序上工作。这样我就可以只用一个语言堆栈来开发跨平台的应用程序。

如果我想做一个原生应用，我可能会选择 C++之类的东西。我认为这是本地化和跨平台的最佳选择。但是 C++是一种很难掌握的语言。然而，Hello World 应用程序并不是 125MB。

然后，当我实际使用 C++开发应用程序时，我需要一个 GUI 框架。这没什么大不了的。但是我想用什么样的材料制作一个漂亮的图形用户界面呢？祝你好运。

所以，如果我看一看，很难决定什么是更好的。我想开始这个讨论，你喜欢什么？或者你会把桌面应用程序放在一边吗？