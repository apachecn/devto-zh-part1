# 访问 boot2docker 容器 IP

> 原文：<https://dev.to/adamkdean/access-boot2docker-container-ip-5hml>

当您运行 boot2docker 时，您的所有容器都将在那个 VM 上运行，而不是在您的本地机器上。因此，默认情况下，您将无法通过它们的容器 IP 访问它们。

要访问它们，请确保 boot2docker 正在运行，并运行:

```
$ sudo route -n add 172.17.0.0/16 `boot2docker ip` 
```

Enter fullscreen mode Exit fullscreen mode

这告诉您的机器将 docker IP 子网上的所有流量定向到运行 boot2docker 的 IP 地址。