# 从命令行启动 IIS Express

> 原文：<https://dev.to/robinvanderknaap/starting-iis-express-from-the-command-line-f8b>

> 我使用 IIS express 已经有一段时间了，但只是作为 Visual Studio 中的一个集成 web 服务器。今天我偶然发现了 IIS Express 的一个我不知道存在的特性。您可以从命令行启动 IIS express 并动态托管 web 应用程序。不需要 Visual Studio。_

要从命令行启动 IIS Express，首先导航到 IIS Express 安装文件夹:

```
c:\Program Files\IIS Express 
```

Enter fullscreen mode Exit fullscreen mode

或者对于 64 位:

```
c:\Program Files (x86)\IIS Express 
```

Enter fullscreen mode Exit fullscreen mode

使用以下命令，您可以在端口 9090 上的指定路径中托管应用程序:

```
iisexpress /path:c:\myapp\ /port:9090 
```

Enter fullscreen mode Exit fullscreen mode

当你想快速测试一个从互联网上下载的应用程序时非常方便。在我的情况下，一个 Umbraco CMS 5.0 的测试版，我在几秒钟内就安装并运行了它。

要获得 IIS Express 的所有命令行选项的更完整的概述，请阅读这篇文章。

当你不想每次启动 IIS Express 时都打开命令提示符时，Chris McLeod 使用一些注册表编辑创建了一个 [shell 扩展](http://chrismcleod.net/iis-express-website-here-shell-extension)，将“IIS Express Website Here”添加到你的上下文菜单中。它在选择的目录中启动 IIS Express，使事情变得更加简单。