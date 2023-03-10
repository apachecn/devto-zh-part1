# 模拟不良网络

> 原文：<https://dev.to/samwho/emulating-bad-networks-38cd>

最近，我一直在研究为程序运行创造困难环境的方法。最相关的环境之一是糟糕的网络。通过现成工具的组合，可以模拟恶劣的网络条件，并在这些条件下独立运行一个进程。

# 网上

创造这种环境的最重要的工具叫做 [NETEM](https://wiki.linuxfoundation.org/networking/netem) 。它代表“网络仿真”，是一个内核模块，可以通过一个叫做`tc`的工具来控制。

下面是一个命令示例:

```
$ tc qdisc add dev eth0 root netem delay 100ms 
```

你必须以特权用户的身份运行。

这非常简单地给通过您的`eth0`接口的任何数据包增加了 100 毫秒的延迟。

## 等等，我运行时出错了…

你有没有收到 RTNETLINK 回复的奇怪信息？不要担心，这些错误可以被修复，但它可能会变得有点复杂。

如果你得到的错误是“不允许操作”，那是因为你需要以 root 用户身份运行命令。在它的开头加上“sudo”应该就可以了。

如果你得到一个关于“没有这样的文件或目录”的错误，那么你可能需要编译一个定制的内核。

## NETEM 内核模块

为了让 NETEM 工作，它需要一个模块加载到您的内核中。这个模块叫做`sch_netem`(`sch`位是“调度程序”的缩写)，你可以通过运行
来测试它是否存在于你的系统中

```
$ lsmod | grep netem 
```

如果该命令有一些输出，这可能意味着该模块存在，您可以像这样将它加载到您的内核中:

```
$ modprobe sch_netem 
```

同样，它需要以 root 用户身份运行才能工作。

如果您没有从`lsmod`命令得到任何输出，您可能需要编译一个定制的内核。我运行的是 Arch Linux，我找不到比这更好的方法了。我在任何地方都找不到`sch_netem`模块，所以我选择了核选项，并把它编译成一个定制的内核。这样做的步骤可以在[内核/Arch 构建系统](https://wiki.archlinux.org/index.php/Kernels/Arch_Build_System) <sup id="fnref1">[1](#fn1)</sup> 中找到。

# 网络名称空间

模拟非常糟糕的网络条件固然很好，但是如果我们不得不降低整个机器的性能来做到这一点，那就没什么好处了。

输入网络名称空间。

网络名称空间是 Linux 网络堆栈的逻辑副本。当您创建网络命名空间时，您正在创建一个可以以任何方式修改的网络堆栈，而无需修改默认的网络堆栈。然后，您可以在这个网络命名空间而不是默认命名空间中运行程序，它们将使用您应用于该命名空间的任何网络配置。

我们要做的是创建一个网络命名空间，为其分配一个虚拟以太网接口，然后通过该虚拟以太网接口将数据包路由到我们的真实以太网接口，以便为其提供互联网连接。当我们做到这一点时，我们将能够将 NETEM 规则应用于虚拟以太网接口，而不会篡改我们的真实以太网接口。

> **注意:**从现在开始运行的所有命令都假设你以 root 用户身份运行它们。这就是说，如果您想让它们工作，您应该在本节中的所有命令前面加上`sudo`。

**步骤 1:创建网络名称空间**

```
$ ip netns add bad 
```

我们将它命名为“bad ”,以表明它的用途。我们将在其他命令中使用这个名称。

**第二步:创建虚拟以太网接口**

```
$ ip link add veth-a type veth peer name veth-b 
```

我们已经创建了一个名为“veth-a”的虚拟以太网接口，并与另一个名为“veth-b”的接口进行了对比。这允许两个接口相互通信。这个 veth 对的目的是将“坏的”名称空间连接到全局名称空间，并允许它与互联网通信。

**步骤 3:将 veth-a 移动到“坏的”名称空间**

```
$ ip link set veth-a netns bad 
```

这将 veth-a 推入“坏”名称空间，这是让“坏”名称空间能够与互联网通信的必要步骤。

为了演示我在这里的意思，试着从“坏的”名称空间中 ping 一些东西:

```
$ ip netns exec bad ping google.com 
```

你会注意到你不能，`ping`抱怨“名称或服务未知”。这是因为网络数据包在这个名称空间中无处可去，所以 google.com 的初始 DNS 查找失败。我们将在接下来的步骤中解决这个问题。

**第四步:调出有自己 IP 地址的虚拟接口**

```
$ ip netns exec bad ifconfig veth-a up 192.168.163.1 netmask 255.255.255.0
$ ifconfig veth-b up 192.168.163.254 netmask 255.255.255.0 
```

接口需要 IP 地址才有用，虚拟接口也不例外。这些命令为每一对分配了自己的 IP 地址。请注意，我们必须通过在“bad”名称空间中运行一个命令来设置 veth-a 的地址。这是因为该接口根本不存在于默认的名称空间:

```
$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:1d:f9:08 brd ff:ff:ff:ff:ff:ff
3: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:c0:c3:a9 brd ff:ff:ff:ff:ff:ff
10: veth-b@if11: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state LOWERLAYERDOWN mode DEFAULT group default qlen 1000
    link/ether da:51:a5:51:27:3a brd ff:ff:ff:ff:ff:ff link-netnsid 0

$ ip netns exec bad ip link
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
11: veth-a@if10: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether fe:79:84:95:57:80 brd ff:ff:ff:ff:ff:ff link-netnsid 0 
```

**步骤 5:为 veth-a**
添加默认网关

```
$ ip netns exec bad route add default gw 192.168.163.254 dev veth-a 
```

在我们运行这个命令之前，目的地为 veth-a 的数据包无处可去。如果不存在其他路由规则(事实上也不存在)，添加默认网关可以让它们有地方可去。所以这个命令告诉他们转到 veth-b，这个命令将我们的“坏”名称空间链接到默认名称空间。

然而，我们仍然无法与互联网通信，这是因为我们与互联网的连接是通过物理以太网接口完成的。对我来说，这是`enp0s3`因为 Arch Linux 使用 systemd，systemd 使用一种接口命名方案，这种方案被设计成对于具有许多物理接口的机器在重启时更加一致。你可以在这里阅读更多。如果你使用无线网络，你的接口可能是`eth0`或`wlan0`。

**步骤 6:从 veth-b 转发数据包到你的物理接口**

```
$ echo 1 > /proc/sys/net/ipv4/ip_forward
$ IFACE=enp0s3
$ IP=$(ip addr show dev ${IFACE} | grep -Po 'inet \K[\d.]+')
$ iptables -t nat -A POSTROUTING -s 192.168.163.0/24 -o ${IFACE} -j SNAT --to-source ${IP} 
```

这里发生了很多事情，让我们来分析一下。

```
$ echo 1 > /proc/sys/net/ipv4/ip_forward 
```

默认情况下，大多数发行版中的内核不会启用 IP 数据包转发。这就是该命令的全部功能:启用数据包转发。我们将需要它在接口之间发送数据包。

```
$ IFACE=enp0s3
$ IP=$(ip addr show dev ${IFACE} | grep -Po 'inet \K[\d.]+') 
```

这将设置 shell 变量来描述我们的物理接口的名称及其在本地网络上的 IP 地址。在继续之前确保它们有效:

```
$ echo $IP
10.0.2.15 
```

您可能会在这里看到一个不同的 IP 地址。只要它在那里，并且变量不为空，您就可以安全地继续操作。

```
$ iptables -t nat -A POSTROUTING -s 192.168.163.0/24 -o ${IFACE} -j SNAT --to-source ${IP} 
```

这是这一步的重点。我们在这里所做的是设置一个 iptables 规则，它会将来自 192.168.163.0/24 子网的任何数据包转发到我们的物理接口，并将数据包的源地址重写为我们的物理接口的地址。这具有通过我们的物理接口转发来自我们的“坏”名称空间的分组的效果。

这就完成了设置，现在您应该能够`ping`通过“坏的”名称空间:

```
$ ip netns exec bad ping google.com
PING google.com (216.58.198.174) 56(84) bytes of data.
64 bytes from lhr25s10-in-f14.1e100.net (216.58.198.174): icmp_seq=1 ttl=55 time=14.3 ms
64 bytes from lhr25s10-in-f14.1e100.net (216.58.198.174): icmp_seq=2 ttl=55 time=14.4 ms
64 bytes from lhr25s10-in-f14.1e100.net (216.58.198.174): icmp_seq=3 ttl=55 time=14.2 ms
^C
--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 14.297/14.358/14.415/0.048 ms 
```

# 通过`tc`使用 NETEM

NETEM 网站有很多例子，我不会在这里重复，但他们没有展示的是，你可以将多个效果链接在一起，创建一些真正可怕的网络。比如:

```
$ tc qdisc add dev veth-b root netem delay 200ms 400ms 25% loss 15.3% 25% duplicate 1% corrupt 0.1% reorder 5% 50%
$ ip netns exec bad ping google.com
PING google.com (216.58.206.110) 56(84) bytes of data.
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=1 ttl=55 time=498 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=2 ttl=55 time=79.2 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=3 ttl=55 time=227 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=4 ttl=55 time=18.5 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=5 ttl=55 time=445 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=6 ttl=55 time=92.7 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=7 ttl=55 time=318 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=8 ttl=55 time=60.6 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=9 ttl=55 time=84.7 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=10 ttl=55 time=519 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=11 ttl=55 time=14.3 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=12 ttl=55 time=14.6 ms
64 bytes from lhr25s14-in-f14.1e100.net (216.58.206.110): icmp_seq=13 ttl=55 time=37.6 ms
^C
--- google.com ping statistics ---
13 packets transmitted, 13 received, 0% packet loss, time 12017ms
rtt min/avg/max/mdev = 14.355/185.570/519.629/186.193 ms 
```

## 等等，什么是“qdisc”？

它代表“排队纪律”。内核为机器上的每个网络接口维护一个数据包队列，有各种方式对数据包进行排队，以考虑服务质量(QoS，赋予某些数据包比其他数据包更高的优先级)。NETEM 通过公开一个插入延迟、丢失和其他有趣事情的 qdisc 来插入这个架构。

# 把所有这些放在一起

上面的命令列表相当复杂和棘手，所以我把它们放在一个大脚本中:

```
#!/usr/bin/env bash

IFACE="enp0s3"
IP=$(ip addr show dev ${IFACE} | grep -Po 'inet \K[\d.]+')
NSNAME="bad"

VETHA="veth-a"
VETHB="veth-b"
VETHPREFIX="192.168.163.0/24"
VETHNETMASK="255.255.255.0"
VETHAIP="192.168.163.1"
VETHBIP="192.168.163.254"

ip netns add ${NSNAME}
ip link add ${VETHA} type veth peer name ${VETHB}
ip link set ${VETHA} netns ${NSNAME}
ip netns exec ${NSNAME} ifconfig ${VETHA} up ${VETHAIP} netmask ${VETHNETMASK}
ifconfig ${VETHB} up ${VETHBIP} netmask ${VETHNETMASK}
ip netns exec ${NSNAME} route add default gw ${VETHBIP} dev ${VETHA}
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -s ${VETHPREFIX} -o ${IFACE} -j SNAT --to-source ${IP}
tc qdisc add dev ${VETHB} root netem delay 200ms 40ms 25% loss 15.3% 25% duplicate 1% corrupt 0.1% reorder 5% 50% 
```

我确信它可以更漂亮、更结实，但目前它对我来说还行。

* * *

1.  注意，如果你在虚拟机上做这个，你还需要重新编译 virtualbox 内核模块:`$ pacman -S virtualbox-guest-dkms`。 [↩](#fnref1)