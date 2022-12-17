# 如何使用 Chromium 和 PyInstaller 将 Web 应用程序转换成桌面应用程序

> 原文：<https://dev.to/tryexceptpass/how-to-turn-a-web-app-into-a-desktop-app-using-chromium-and-pyinstaller-24cn>

打包和分发你的应用程序听起来很简单。只是软件而已。但是实际操作起来，还是挺有挑战性的。

我一直在开发一个叫做 Sofi 的 Python 模块，它可以生成用户界面。它可以在使用标准的单页 web 技术的同时提供桌面的感觉。为了灵活起见，我设计了两种分发方式:浏览器内和可执行文件。

在浏览器中运行，它的功能很像一个普通的网页。您可以通过打开一个文件来加载它，或者从您的 shell 启动它。我还构建了一个可执行文件，作为打包的应用程序运行，独立且没有外部要求。

随着时间的推移，当我在 Atom——我最近选择的编辑器——中编写代码时，我想起 Atom 实际上是一个浏览器。它使用 Node.js 作为后端，使用电子框架作为用户界面。这启发我开始探究 Electron 的内部，希望找到他们如何解决桌面打包的例子和最佳实践。

[读下去...](https://tryexceptpass.org/article/how-to-turn-a-web-app-into-a-desktop-app/)