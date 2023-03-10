# 使用 Netcat 进行端口扫描

> 原文：<https://dev.to/adamkdean/port-scanning-with-netcat-625>

Netcat，或`nc`，是 linux 的一个优秀工具。它的一大优势是端口扫描。

假设我们要扫描从 1 到 1000 的开放端口，我们可以这样做:

```
nc -n -z -w 1 192.168.1.2 1-1000 
```

Enter fullscreen mode Exit fullscreen mode

`-n`阻止 DNS 查找。`-z`将阻止 Netcat 接收任何数据。`-w 1`将超时设置为 1 秒。

您也可以使用`-v`使它变得冗长，但是这会模糊成功尝试的可见性，运行它时不会显示。

上述命令的输出示例如下:

> 连接 192.168.1.2 80 端口[tcp/ *]成功！
> 
> 连接 192.168.1.2 427 端口【tcp/* 成功！
> 
> 连接 192.168.1.2 515 端口【tcp/*】成功！

这么有用！