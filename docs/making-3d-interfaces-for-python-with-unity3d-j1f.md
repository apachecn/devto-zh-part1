# 用 Unity3D 制作 Python 的 3D 界面

> 原文：<https://dev.to/tryexceptpass/making-3d-interfaces-for-python-with-unity3d-j1f>

### 使用 Sofi 和 WebSockets 命令游戏引擎

不久前，我写了一篇关于使用游戏引擎作为前端接口的文章。我想在 Python 生态系统中启用丰富的接口，这些接口可用于虚拟和增强现实。从那以后，我能够在 [Sofi](https://www.github.com/tryexceptpass/sofi) 的基础上构建一些基本概念，我在这里分享它们。

### 后端

Sofi 作为一个 WebSocket 服务器工作，可以从客户端发出命令和处理事件。它旨在简化前端 web 技术作为 Python 后端接口的使用。你甚至可以[把它打包成一个桌面应用](https://tryexceptpass.org/article/how-to-turn-a-web-app-into-a-desktop-app)，拥有桌面的外观和感觉。

它的功能是将用户发送到一个网页，该网页提供了打开 WebSocket 客户端所需的基本信息。然后，这个客户机注册处理服务器命令的处理程序，告诉它如何改变 DOM 或响应事件。所有的逻辑都在 Python 中，网页是用户界面。

[读下去...](https://tryexceptpass.org/article/making-3d-interfaces-for-python-with-unity3d/)