# 在 resolv.conf 中找到本地 DNS 解析程序，容器无法使用它

> 原文：<https://dev.to/adamkdean/local-dns-resolver-found-in-resolv-conf-and-containers-can-t-use-it-55ih>

我一直在玩 Docker 的一个项目的一部分，并遇到了这个恼人的，但重要的信息:

> 警告:在 resolv.conf 中找到本地(127.0.0.1) DNS 解析程序，容器无法使用它。使用默认的外部服务器:[8.8.8.8·8.8.4.4]

问题？`/etc/resolv.conf`里面有`nameserver 127.0.0.1`(或`nameserver 127.0.1.1`)。

我发现 Ubuntu 14.04 LTS 使用了一个名为`dnsmasq`的守护进程，它将 DNS 流量路由到 DHCP 提供的 DNS 服务器。

虽然这对于 Docker 来说很好，但是这条消息非常烦人。

我在`/etc/NetworkManager/NetworkManager.conf`中禁用了 dnsmasq:

```
$ sudo nano /etc/NetworkManager/NetworkManager.conf

#dns=dnsmasq 
```

Enter fullscreen mode Exit fullscreen mode

然后，我将公共 Google DNS 服务器添加到`/etc/dhcp/dhclient.conf`:

```
$ sudo nano /etc/dhcp/dhclient.conf

prepend domain-name-servers 8.8.8.8,8.8.4.4; 
```

Enter fullscreen mode Exit fullscreen mode

然后你只需要重启网络管理器:

```
$ sudo restart network-manager 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以启动 Docker 容器，而不需要那个讨厌的消息了！