# Windows10 Linux 子系统 WSL -需要营销 2017-12-06

> 原文：<https://dev.to/annetheagile/windows10-linux-subsystem-wsl---marketing-needed-2017-12-06-dc7>

### Windows10 Linux 子系统(WSL)发布

dev.to [9]文章分享了如何使用非 bash 终端运行 WSL。我已经有一段时间没有研究 WSL 了，但是当我听说它即将到来时，我真的很兴奋。巴什对我有好处。

### WSL 可能不是为 Docker 工作负载构建的

最初，我认为我们可以在子系统中使用“真正的”linux docker，然后从 windows 管理它。根据 FAQ [16]，这似乎不是他们所追求的用例。相反，他们似乎想支持在本地构建 rails 应用？也许[17]是他们想要支持的一个例子？尽管这看起来很复杂。

### 让我们进一步了解微软产品/营销如何区分 WSL

我不知道应该如何考虑使用 WSL，因为完全的网络支持似乎不在他们的设计目标中。也许我们会得到对我的 github 评论[13.1]的回复。
安妮

参考文献*与我的伪隐晦笔记*
9。[]提示 WIN10 启动并运行 WSL，今天阅读的文章摘要 2017-12-06Wed。；1.WSL Linux 子系统现已退出测试！；2.要使用它，做一些安装，打开它，从商店选择一个 Linux[Ubuntu+now，debian like TBA]；3.注意哪个文件系统是哪个文件系统；4.[其他地方] USB 文件是可以的；；；；；；；；；；；；；；；；x .启动并运行 Linux 的 Windows 子系统；；[https://dev . to/wine baths/get-up-and-running-with-the-windows-subsystem-for-Linux-8oc](https://dev.to/winebaths/getting-up-and-running-with-the-windows-subsystem-for-linux-8oc)

13.1.[]我的备注“我们需要营销/产品输入”;在；nmap 不工作问题# 1349 Microsoft/WSL；；[https://github . com/Microsoft/WSL/issues/1349 # issue comment-349621218](https://github.com/Microsoft/WSL/issues/1349#issuecomment-349621218)

16.[] ;WSL 常见问题；令我困惑；；；；WSL 的目标不是支持 GUI 桌面或应用程序(如 Gnome、KDE 等)。);此外，尽管您将能够运行许多流行的服务器应用程序(例如 Redis)，但我们不建议将 WSL 用于服务器场景——微软为在 Azure、Hyper-V 和 Docker 中运行生产 Ubuntu 工作负载提供了各种解决方案。；；；；；；WSL 的好处之一是能够在 Windows 和 Linux 应用程序或工具中使用同一个文件。；；；；；十、常见问题(FAQ)；；[https://msdn.microsoft.com/en-us/commandline/wsl/faq](https://msdn.microsoft.com/en-us/commandline/wsl/faq)

17.[]RUBY RAILS；WIN DOCKERidk，不确定为什么/需要复杂？但是可能会回答 WSL FAQ，person 似乎喜欢将它用于 ruby rails 开发；；我们仍然需要在 WSL 中安装 Docker，因为它将为我们提供对 Docker CLI 的访问。我们只是不想启动服务器。；；；虽然 Docker 守护进程不能直接在 WSL 上运行，但是您可以使用 Docker CLI 连接到通过 Docker for Windows 或您创建的任何其他虚拟机运行的远程 Docker 守护进程。真的很牛逼！使用这种方法，非常大的 Rails 应用程序可以在 200 毫秒内响应(或者当必须编译 10，000 多行 Javascript 和 SCSS 时，大约 5 秒)。这也包括已装载的卷！换句话说，它对于日常的 web 开发非常有用。；；；；；；；；；；x .为 Windows 和 WSL 设置 Docker 以使其完美运行——Nick Janetakis；；[https://nickjanetakis . com/blog/setting-up-docker-for-windows-and-wsl-to-work-完美无缺](https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly)

[meta]日期:2017-12-06Wed.08:07:10EST
描述/展开:Hmmm。Windows Linux 子系统不能使用网络工具——故意的？