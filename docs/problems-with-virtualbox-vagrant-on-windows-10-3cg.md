# Windows 10 上 VirtualBox+vagger 的问题

> 原文：<https://dev.to/alexhyettdev/problems-with-virtualbox-vagrant-on-windows-10-3cg>

我是流浪者的忠实粉丝。当我在寻找创建一个可以在各种计算机之间转移的开发环境的方法时，我第一次发现了 vagger(我有一台台式机和一台笔记本电脑，我经常在上面工作)。我甚至想过在一个快速的 USB 3 棒上安装一个 Linux 发行版来随身携带。就在那时，当我在寻找完美的开发环境时，我发现了流浪者。然而，我还没有得到流浪的 Windows 10 工作，直到现在。

我最后一次使用它的时候，它工作得很好，主要用于 WordPress 主题开发。我已经有 6 个月没碰它了，但是在输入“流浪”和“等待”之后，我很快就收到了这个消息:

```
VBoxManage.exe: error: Failed to create the host-only adapter
VBoxManage.exe: error: Code E_FAIL (0x80004005) - Unspecified error (extended info not available)
VBoxManage.exe: error: Context: "int __cdecl handleCreate(struct HandlerArg *,int,int *)" at line 66 of file VBoxManageHostonly.cpp 
```

Enter fullscreen mode Exit fullscreen mode

当你刚开始重新投入某事时，这不是你真正想看到的。很自然，我做了每一个值得尊敬的软件开发人员在遇到他们从未见过的错误时都会做的事情🙂。原来原因似乎是 Windows 10 和 VirtualBox 的问题。

首先我升级了 VirtualBox 和 vagger，所以我现在运行的是 VirtualBox 5.0.10 r104061 和 vagger 1 . 7 . 4。我做了一个流浪了，得到了同样的错误。

我在 VirtualBox 的网站上找到了下面这张[票](https://www.virtualbox.org/ticket/14040)，并下载了 VBox-Win10-fix-14040.exe 的变通应用[。这必须以管理员身份运行，并在设置虚拟机时保持运行。](https://www.virtualbox.org/attachment/ticket/14040/VBox-Win10-fix-14040.exe)

这样做给了我一个稍微不同的错误:

```
Command: ["hostonlyif", "ipconfig", "VirtualBox Host-Only Ethernet Adapter #7", "--ip", "192.168.56.1", "--netmask", "255.255.255.0"]

Stderr: VBoxManage.exe: error: The host network interface with the given name could not be found
VBoxManage.exe: error: Details: code E_INVALIDARG (0x80070057), component Host, interface IHost, callee IUnknown
VBoxManage.exe: error: Context: "FindHostNetworkInterfaceByName(name.raw(), hif.asOutParam())" at line 218 of file VBoxManageHostonly.cpp
VBoxManage.exe: error: Could not find interface 'VirtualBox Host-Only Ethernet Adapter #7' 
```

Enter fullscreen mode Exit fullscreen mode

我认为这可能是一个权限问题，于是我在可执行文件的兼容性设置中设置了“流浪者”作为管理员运行，并在管理控制台中运行了“流浪者”,此外还运行了上面的变通办法 exe，这样就成功了。

如果你感兴趣的话，我使用稍微修改过的版本，增加了节点。你可以在[我的 GitHub 页面](https://github.com/alexhyett/vagrantpress)找到。

更新:这个问题已经在最新的 VirtualBox 测试版本中得到解决，可以从[这里](https://www.virtualbox.org/wiki/Testbuilds)下载。我目前使用的是 VirtualBox 5.0.11.104704，但任何版本都可以。您也不需要以管理员身份运行，除非您运行的是类似于需要管理员访问权限才能写入主机文件的流浪者 hostupdater 这样的程序。