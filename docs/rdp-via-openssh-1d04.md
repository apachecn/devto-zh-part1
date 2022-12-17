# 通过 OpenSSH 实现 RDP

> 原文：<https://dev.to/adamkdean/rdp-via-openssh-1d04>

我写过关于通过 SSH 连接到 RDP 的文章，但是那依赖于 PuTTY。因为我现在使用的是 OSX，所以我可以访问 OpenSSH。这使得事情变得更容易。

```
ssh -L <localport>:<remotehost>:<remoteport> <sshserver> 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
ssh -L 8001:192.168.0.1:3389 adamkdean.co.uk 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以用 RDP 连接到本地端口 8001。