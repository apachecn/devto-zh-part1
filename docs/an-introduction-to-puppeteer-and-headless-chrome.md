# 木偶师和无头铬合金介绍

> 原文：<https://dev.to/mohamed3on/an-introduction-to-puppeteer-and-headless-chrome>

[无头 Chrome](https://chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md) 是 Chrome 59 (Linux 和 Mac)和 Chrome 60 (Windows)中新发布的功能。它允许在不启动浏览器窗口的情况下以编程方式测试网站，从而使自动化测试更加容易，这反过来又让您更有信心在不破坏任何东西的情况下更改您的应用程序。

## 首先，什么是‘无头’？

Headless 基本上意味着“没有 GUI”，这意味着在 Chrome 的情况下，你将使用可编程的 API，而不是可以与之交互的 GUI。无头模式的一个很好的例子是当您使用 SSH 处理服务器，并使用 shell 命令进行所有交互时。

## 用木偶戏制作无头铬

木偶师是由 Chrome 团队制作的一个 npm 包，通过一个方便的高级 API 来轻松地与 Headless Chrome 交互。
它是一个新发布的模块，与 PhantomJS 或 Selenium 非常相似，但它的不同之处在于使用了最新版本的 Chrome，并使用 headless 模式作为默认模式。

## 用无头 Chrome 和木偶师可以运行什么样的测试？

木偶师的一个很好的用例是对你的 UI 进行自动化测试，通过截屏或者导出到 PDF。由于 headless Chrome 让你能够做普通浏览器能做的任何事情，你可以用它来自动化你系统的整个用例(端到端测试)。例如，用户登录、表单提交、按钮点击、页面导航等等。
你还可以使用木偶师定期抓取网站，并将你想要提取的相关信息存储在数据库中，这类似于你可以使用 Python 的[美汤](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)包所做的事情。

## 包装完毕

总而言之，Puppeteer 是一个软件包，它允许您以自动化的方式以编程的方式处理网页，无论是对网页进行截图，还是将其导出到 pdf，或者单击按钮和填写表单，或者提取/抓取页面内容以供以后检查。它为你提供了一个非常强大的 API，允许你在一个成熟的浏览器中做任何你能做的事情，而不需要浏览器。如果你有兴趣并想了解更多关于无头浏览器的信息，请查看[这篇文章](https://developers.google.com/web/updates/2017/04/headless-chrome)。如果你想看如何使用木偶师的例子，点击这里的。