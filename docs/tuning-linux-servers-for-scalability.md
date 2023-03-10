# 调整 Linux 服务器的可伸缩性

> 原文：<https://dev.to/madhur/tuning-linux-servers-for-scalability>

在使用技术产品时，关注性能和可扩展性是我个人和职业的主要目标之一。

该服务器可以是任何基于 Linux 的服务器，如 CentOS(T0)CentOS(T1)或 Debian(T2)的衍生产品，如 Ubuntu(T4)的 T5。

在这篇文章中，我将概述我在扩展 Linux 服务器方面的经验。这里的缩放意味着许多事情，例如

*   能够一次打开许多文件。这里，文件通常可以应用于开放端口、线程等概念，而不是必需的物理文件

*   能够处理许多并发的网络连接

了解 Linux 操作系统和相关概念，如 [Iptables](https://en.wikipedia.org/wiki/Iptables) 是先决条件。

## 打开文件

对于任何 linux 生产服务器，我们都需要保持较高的文件限制。使用`ulimit -a`检查当前值

我们可以使用`/etc/security/limits.conf`
来配置这个限制

```
 *  hard  nofile  300000  *  soft  nofile  300000  tomcat  hard  nofile  300000  tomcat  soft  nofile  300000 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们还可以如上所示指定每个用户的限制(tomcat 用户的特殊限制)

正在运行的进程的文件描述符限制可以在 Max open files 下的以下文件中看到。

```
 $  cat  /proc/<pid>/limits  Max  open  files  30000 
```

Enter fullscreen mode Exit fullscreen mode

## 短暂的港口

增加应用程序可用的电子邮件端口数量。默认值为`32768 - 61000`。

## 时间 _ 等待状态

TCP 连接会经历许多状态，最后一个是`TIME_WAIT`状态。默认的`TIME_WAIT`超时是 2 分钟，这意味着如果您每秒接收超过 400 个请求，您将用完可用的端口，或者如果我们回头看看 nginx 如何做代理，这实际上转化为每秒 200 个请求。

这些参数可以使用`/etc/sysctl.conf`
中的这些设置进行调整

```
 net.ipv4.ip_local_port_range  =  18000  65535  net.ipv4.netfilter.ip_conntrack_tcp_timeout_time_wait  =  1 
```

Enter fullscreen mode Exit fullscreen mode

## 连接追踪

我们研究的下一个参数是连接跟踪。这是使用`iptables`的副作用。因为`iptables`需要允许已建立的 HTTP 和 ssh 连接之间的双向通信，所以它需要跟踪哪些连接被建立，并将它们放入连接跟踪表中。这张桌子变大了。并成长。并成长。

您可以使用`sysctl net.netfilter.nf_conntrack_count`查看该表的当前大小，使用`sysctl net.nf_conntrack_max`查看其限制。如果 count 超过 max，你的 linux 系统将停止接受新的 TCP 连接，你永远也不会知道。发生这种情况的唯一迹象是隐藏在`/var/log/syslog`某处的一行字，表示您没有连接跟踪条目。一句台词，一次，第一次发生的时候。

更好的指示是计数是否总是非常接近最大值。你可能会想，“嘿，我们已经完全正确地设置了 max。”但是你错了。

你需要做的(或者至少是你首先想到的)是增加 max。

请记住，这个值越大，内核将使用越多的内存来跟踪这些条目。您的应用程序可以使用的 RAM。

我们开始沿着这条路走下去，增加 net.nf_conntrack_max，但很快我们就每天都在推高它。进入那里的连接永远不会出来。

## 套接字上挂起连接的最大数量

在我们的一些初始负载测试中，我们遇到了一个奇怪的问题，我们无法一次打开大约 128 个以上的并发连接。

经过一些调查，我们了解了以下内核参数。

```
 net.core.somaxconn 
```

Enter fullscreen mode Exit fullscreen mode

这个内核参数是等待应用程序接受的 TCP 连接的积压量。如果连接指示在队列已满时到达，则连接被拒绝。在大多数现代操作系统中，该参数的默认值是 128。

在`/etc/sysctl.conf`中提高这个限制帮助我们摆脱了 Linux 机器上的“连接拒绝问题”。

## JVM 线程计数

在我们第一次允许很大比例的生产流量冲击我们的服务器几个小时后，我们被提醒负载平衡器无法连接到我们的一些机器。经过进一步调查，我们在服务器日志中发现了以下内容。

```
 java.lang.OutOfMemoryError:  unable  to  create  new  native  thread 
```

Enter fullscreen mode Exit fullscreen mode

如果您达到了 JVM 线程限制，那么很可能您的代码中存在需要修复的线程泄漏。但是，如果您发现所有的线程实际上都在做有用的工作，有没有办法调整系统，让您创建更多的线程并接受更多的连接？

一如既往，答案是有趣。讨论可用内存如何限制 JVM 上可以创建的线程数量是很有趣的。线程的堆栈大小决定了可用于静态内存分配的内存。因此，线程的绝对理论最大数量是进程的用户地址空间除以线程堆栈大小。然而，事实是 JVM 也使用内存在堆上进行动态分配。通过对一个小的 Java 进程进行一些快速测试，我们可以验证为堆分配的内存越多，堆栈可用的内存就越少。因此，线程数量的限制随着堆大小的增加而减少。

总而言之，您可以通过减少每个线程的堆栈大小`(-Xss)`或者减少分配给堆的内存`(-Xms, -Xmx).`来增加线程数量限制