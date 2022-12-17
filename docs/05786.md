# Ubuntu 上的狂欢

> 原文：<https://dev.to/youssefmrini/bash-on-ubuntu>

你知道你可以在 Windows 10 上使用 Bash Shell 吗？你不是在做梦，由于微软和 Ubuntu 的创造者 Canonical 的合作，Bash 可以在 Windows 10 上使用。在这篇文章中，我将向你展示如何在 Windows 10 上安装 Bash Shell。
Windows Subsystem for Linux(WSL)为您提供了一个命令行开发环境，与 Unix 系统上的开发环境完全相同，无需虚拟机。
在 Windows 10 Creators Update 中，对 Windows 上的 Bash 进行了多项改进。包括从 Bash 中启动 Windows 应用程序&工具的能力
微软还与 SUSE 和 Fedora 合作，将他们的 Linux 发行版带到 Windows 商店& Windows 子系统 for Linux (WSL)

要在 Windows 10 中安装 Bash，请遵循以下步骤:
启用开发者模式
进入设置|更新和安全|面向开发者
选择开发者模式

当您启用开发人员模式时，您会收到一条警告，因为您将能够在调试模式下从 Visual Studio 下载和运行应用程序。您必须启用开发人员模式才能在 Windows 上使用 Bash Shell。你可以在 Docs.microsoft.com 了解更多关于开发者模式的信息

打开 Windows 子系统 for Linux 功能
打开控制面板
转到程序|程序和功能|打开或关闭 Windows 功能

选择 Windows 功能 Linux 的 Windows 子系统

运行 Bash
打开命令提示符
运行 bash
你会被提示接受许可。键入 y 接受许可。

第一次在 Windows 上安装 Bash 时，系统会提示您创建一个 UNIX 用户名和密码。此 Unix 用户名和密码与您的 windows 用户名和密码无关，并且可以不同。

在 Windows 上启动 Bash

在 Windows 上使用在 cmd/PowerShell 命令提示符
下运行 bash 或者使用开始菜单快捷方式启动 Bash。

Windows 上 Ubuntu 的开始菜单| Bash

键入命令(如 ls 或 echo)来测试您的安装。

恭喜你在 Windows 上运行 Bash！要了解这个特性的更多信息，请查阅 MSDN 关于 Windows 上 ubuntu 上 Bash 的文档。

推特 [@youssefmrini](https://dev.to/youssefmrini)