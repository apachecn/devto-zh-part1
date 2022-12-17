# 通过 SSH 访问 RDP(PuTTY)

> 原文：<https://dev.to/adamkdean/rdp-via-ssh-putty-3pml>

如果你和我一样，并且你对开放你的服务器到互联网的访问有些偏执，你可能有时会发现你自己需要访问它们，但是不在本地网络上。如果您可以访问 SSH，一个简单的方法就是打开一个隧道。

为此，我们需要:

*   目的服务器的 IP 或主机名和端口(为此，它们是`192.168.1.2`和`3389`
*   通过 SSH 访问本地网络上的服务器
*   本地计算机上的 PuTTY

首先，打开命令提示符，或 MinGW 或别的什么，并导航到您的 PuTTY 路径:

```
cd /c/PuTTY/ 
```

Enter fullscreen mode Exit fullscreen mode

然后连接到 SSH 服务器，指定一个隧道，如下所示:

```
putty.exe -L <localport>:<remotehost>:<remoteport> <sshserver> 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
putty.exe -L 8001:192.168.1.2:3389 adamkdean.co.uk 
```

Enter fullscreen mode Exit fullscreen mode

这将打开一个油灰窗口。验证并打开另一个 cmd/mingw 提示符。在这个例子中，我们现在将使用`mstsc`(远程桌面)通过隧道进行连接。我们在本地机器上打开了端口`8001`,所以让我们连接到它。

```
mstsc -v localhost:8001 
```

Enter fullscreen mode Exit fullscreen mode

如果你做的一切都正确，你现在应该得到一个远程桌面提示。

*2016 年 11 月 2 日更新:*您可以使用常规 ssh 建立隧道连接，如下所示:

```
ssh user@hostname -L <localport>:<host>:<remoteport> -N 
```

Enter fullscreen mode Exit fullscreen mode

`-L`是你的隧道，`-N`是 connect 上没有执行的命令。