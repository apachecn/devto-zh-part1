# 我们测试了 3 个不同的 WordPress 网站上的所有数字海洋水滴

> 原文：<https://dev.to/learntopdown/we-tested-3-different-wordpress-websites-on-all-digitalocean-droplets-70m>

我们对不同的 CPU 型号如何影响 Wordpress 网站性能做了全面的研究。

以下是我们的结论总结。

*   PHP 依赖于 CPU 的处理能力，这意味着如果你需要更好的性能，选择一个具有更好的官方基准分数的 CPU 型号是很重要的——CPU 很重要。
*   CPU 内核的数量不会影响 PHP 应用程序的性能——PHP 不是多线程的。CPU 内核的数量只会帮助您处理更高的流量。
*   PHP 字符串函数比数学函数更昂贵。
*   具有大量 RAM 内存的 VPS 实例对 PHP 性能的影响很小或者没有影响。
*   最好的决定是使用几个带有负载平衡器的高 CPU VPS 实例，并为 MySQL DB 使用一个单独的实例
*   一个标准的 20 美元的 DigitalOcean droplet 会给你一个比 640 美元的 DigitalOcean droplet 更好的 PHP 性能。
*   一个高 CPU $40 的 DigitalOcean droplet 将在 digital ocean droplet 中给你最好的 PHP 性能。
*   专用 vCPU 为我们提供了更稳定的性能——没有大的加载时间峰值
*   糟糕的程序员太贵了。

请看一下[带图表和所有数据的原始研究文章](https://learnwebdevelopment.review/article/how-much-different-cpu-models-affect-wordpress-performance)