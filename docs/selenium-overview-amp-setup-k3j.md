# Selenium 概述和设置

> 原文：<https://dev.to/stephencavender/selenium-overview-amp-setup-k3j>

## 快速硒概述

在这篇文章中，我们将探索硒是什么，以及如何准备我们的环境使用它。

> Selenium 是一个伞状项目，包含一系列工具和库，支持 web 浏览器的自动化。——来自[https://seleniumhq.github.io/docs/index.html](https://seleniumhq.github.io/docs/index.html)

硒使我们能够:

1.  打开浏览器
2.  浏览网页
3.  查找 web 元素
4.  操作 web 元素
5.  运行 JavaScript

### 语言绑定

首先，我们需要得到我们想要的语言绑定。我们的项目中需要下载和引用绑定。一些 IDE 有包管理器来为我们做这件事。我们将在单独的文章中详细讨论如何做到这一点。现在只需要知道我们需要一些语言绑定来让 Selenium 工作。

### [t1 网络驱动程序](#webdrivers)

Selenium 的动力来自于[web drivers](http://docs.seleniumhq.org/docs/03_webdriver.jsp)；这样称呼是因为它们“驱动网络”在接下来的文章中，我们将使用 Chrome、Firefox、Edge 和 IE；我们每辆车都需要司机。

#### 铬

[ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/) 是一个单独的可执行文件，当 Chrome 是所需的浏览器时，由 Selenium 加载。下载最新的驱动程序，并将其放在容易访问的位置。我把我的放在`C:/webdrivers/`里。下载安装最新的 [Chrome](https://www.google.com/intl/en/chrome/browser/desktop/index.html) 。

#### 火狐

FirefoxDriver 内置了 Selenium 语言绑定。下载并安装最新的[火狐](https://www.mozilla.org/en-US/firefox/all/)。Mozilla 正在为他们的浏览器开发一个名为[的新驱动程序。但是，在撰写本文时，它还没有发布。请随意下载最新的驱动程序，并将其放在与 ChromeDriver 相同的位置。](https://developer.mozilla.org/en-US/docs/Mozilla/QA/Marionette)

#### 边缘

当 Edge 被请求作为浏览器时，[微软 WebDriver](https://www.microsoft.com/en-us/download/details.aspx?id=48212) 是由 Selenium 加载的一个单独的可执行文件。下载最新的驱动程序，并将其保存在与其他驱动程序相同的位置。确保您拥有 Edge 的最新更新。

#### IE

IEDriverServer 由 Selenium group 维护和分发。下载最新的驱动程序，并将其放在与其他驱动程序相同的位置。确保你有最新的 IE 更新。

### 添加到路径中

现在我们已经有了所有想要使用的驱动程序，我们需要在 PATH 环境变量中添加一个条目。如果你从未使用过环境变量，我推荐你阅读这个[指南](http://www.howtogeek.com/118594/how-to-edit-your-system-path-for-easy-command-line-access/)。按照说明添加包含您的 web 驱动程序的文件夹。

现在硒将蓄势待发！查看我的另一篇文章，了解您希望使用 Selenium 的环境:

*   [C#，硒& MSTest](https://www.cavender.io/selenium/dotnet-selenium-mstest-quickstart)
*   [JavaScript，硒&摩卡](https://www.cavender.io/selenium/js-selenium-mocha-quickstart)

感谢阅读！如果你觉得这篇文章有帮助，一定要分享，不要犹豫，和我聊聊吧！

最初发布于 [cavender.dev](https://cavender.dev/selenium/selenium-overview-setup/)