# boot2docker DNS 不工作

> 原文：<https://dev.to/adamkdean/boot2docker-dns-not-working-k6f>

如果你在 OSX 上使用 Docker，那么你将使用一个 VM 来运行你的 Docker 服务，因为它需要 Linux，而不是 Unix。其中最受欢迎的似乎是 boot2docker，尽管 dvm 似乎也很受欢迎。还有其他解决方案。

我有一个问题，有时 boot2docker 的 DNS 似乎不工作。

不过，我找到了一些关于如何修复它的说明:

```
$ boot2docker ssh
$ sudo udhcpc
udhcpc (v1.22.1) started
Sending discover...
Sending select for 10.0.2.15...
Lease of 10.0.2.15 obtained, lease time 86400
deleting routers
route: SIOCDELRT: No such process
adding dns 192.168.1.1    
$ sudo /etc/init.d/docker restart
$ exit 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你试图 ping 谷歌，它应该工作！