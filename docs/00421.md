# 为高网络流量配置 PHP-FPM

> 原文：<https://dev.to/jake/configuring-php-fpm-for-high-network-traffic-47le>

[![php-fpm logo](img/cd289c6d4d152f8f93d9da2c3045a749.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u1YZO3x5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gp15idn6v674qdh0ob98.png)

在使用 PHP 时，在高网络流量的服务器上保持恒定的响应时间可能是我职业生涯中最难也是最烦人的事情。从 Apache 转换到 Nginx 是一个巨大的性能升级...在 RPS(每秒请求数)导致堆栈再次溢出之前，这种情况持续了整整半个小时。

我开始浏览栈溢出和谷歌(开发者最好和最坏的朋友/敌人)寻找答案，我发现了几个显示如何解决这个问题的帖子(PHP-FPM 会议)，但没有解释任何事情。作为一名实践开发者，我想知道事情是如何运作的。

最初我的 PHP-FPM 会议看起来是这样的

```
[www]

user = apache
group = apache

listen = 127.0.0.1:9000
listen.allowed_clients = 127.0.0.1

pm = dynamic

pm.max_children = 200

pm.start_servers = 20

pm.min_spare_servers = 10
pm.max_spare_servers = 20

pm.max_requests = 1000 
```

我开始阅读`pm = dynamic`的意思，我发现了这个(在另一个 conf 文件中，哈哈)

> 动态——子进程的数量根据以下指令动态设置。有了这个流程管理，总会有至少 1 个孩子。

继续...200 个孩子都是在启动时产生的吗？他们总是无所事事吗？

是的&是的(我想)。我开始使用`ps aux |grep apache`查看和记录`apache`池的内存使用情况。无论处理多少个请求(0 个请求- 1000 个请求)，总有 200 个孩子活着。不要误解我，我喜欢孩子，但是一次 200 个没有理由的有点多了。

在花了几个小时摆弄我的 PHP-FPM conf 并运行压力测试后，我想出了这个

```
[www]

user = apache
group = apache

listen = 127.0.0.1:9001

listen.allowed_clients = 127.0.0.1

pm = ondemand
pm.max_children = 200
pm.process_idle_timeout = 1s
pm.max_requests = 1000 
```

我运行了另一个压力测试:一分钟 2000 RPS(每秒请求数),平均响应时间从 1000 毫秒到 120 毫秒。

发生了什么事？

PHP-FPM 的 PM(池管理器)在启动时产生了 200 个孩子，即使这些孩子是空闲的，PM 也在使用不必要的资源来管理这些孩子。从`dynamic`到`ondemand`的切换允许孩子在需要的时候产生，并在 1 秒钟不活动后杀死他们。

请随意评论这篇文章，我对 PHP-FPM 的池管理器是如何工作的知之甚少。我觉得它可能对那些陷入 PHP-FPM 困境的人有所帮助。

编辑:这篇文章中的一些信息是不正确的，例如我混淆了`dynamic`和`static`在启动时产生 200 个孩子。然而，我仍然在新的服务器上使用同样的设置，它比我用过的任何其他配置都要好得多。