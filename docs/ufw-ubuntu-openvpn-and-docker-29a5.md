# uff、Ubuntu、OpenVPN 和坞站

> 原文：<https://dev.to/adamkdean/ufw-ubuntu-openvpn-and-docker-29a5>

今天我一直在玩 UFW，Ubuntu，OpenVPN 和 Docker。一些其他的东西，但这篇文章涉及到这些技术，主要是 UFW，Ubuntu 和 Docker。

如果你不知道，就像我不知道一样，当你暴露端口时，Docker 认为一个很好的想法是将一些规则放入 iptables 以允许流量通过。我相信这是有原因的，但是您可能会发现，当您启用 UFW 时，流量仍然可以通过。现在我明白发生了什么，这一切都很有意义，但达到这一点已经占用了我的晚上。

我跟随这个教程学习了如何在 Ubuntu 14.04 上的 Docker 容器中运行 OpenVPN，这是一件轻而易举的事情。真正凸显了 Docker 的优势。此设置为您分配一个 192.168.255.0/24 中的 IP。

我的下一步，现在我有了 VPN 访问，是关闭机器上的 eth0，只允许通过端口 22 & 1194/udp。我不知道的是，在这段时间里，码头工人会成为皇家的讨厌鬼。它会继续绕过我的规则，让我想知道 UFW 到底出了什么问题。没什么，是 Docker 在玩 iptables。

要阻止 Docker 这样做，您必须用- iptables=false 标志启动 Docker 守护进程，或者我想现在叫它 Docker 引擎。要做到这一点，在 Ubuntu 14.04 上，在你最喜欢的文本编辑器中打开`/etc/default/docker`并添加下面一行:

```
DOCKER_OPTS="--iptables=false" 
```

Enter fullscreen mode Exit fullscreen mode

保存它，然后重启 Docker 守护进程/引擎/服务器:

```
sudo restart docker 
```

Enter fullscreen mode Exit fullscreen mode

现在，我不确定现有的容器会发生什么，但是我删除了它们，重新开始。现在，当你开始新的集装箱，将不会有疯狂的规则绕过 UFW。

对于 UFW，我必须添加一些规则。我添加了端口 22 (SSH)和 1194/udp (VPN)。我还添加了一条规则，允许来自 docker0 的所有流量:

```
sudo ufw allow 22
sudo ufw allow 1194/udp
sudo ufw allow in on docker0 to any
sudo ufw enable 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我启动了一些容器，并试图访问它们。禁止进入...打开虚拟专用网，再试一次，结果成功了。我有权限。到那里花了一段时间，但我还是到了。安全进入我的集装箱。

现在我需要洗个长长的热水澡来放松一下。非常感谢码头工人！