# PHP 上 AMQP 客户端的性能。Benchmark `和所有人！

> 原文：<https://dev.to/phpprofi/-amqp---php-benchmark--482g>

[![](img/280db5752a4a8d186a17238bcd0dd6a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JeodhAzd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/275ba737-729e-4a12-aaf2-53498cf63187.jpeg)

t0 amqp interop t1 越来越受欢迎，引发了一系列问题，询问哪个适配器速度最快。在这篇文章中，我想分享我对消息排队性能的调查结果。以后会有类似的“拆台”排队的帖子。

考虑:

*   [php-amqplib](https://github.com/php-amqplib/php-amqplib)
*   [amqp-ext](https://github.com/pdezwart/php-amqp)
*   [兔子](https://github.com/jakubkulhan/bunny)

以及它们的 T0 适配器:

*   [入队/amqp-lib](https://github.com/php-enqueue/enqueue-dev/blob/master/docs/transport/amqp_lib.md)
*   [入队/amqp-ext](https://github.com/php-enqueue/enqueue-dev/blob/master/docs/transport/amqp.md)
*   [入队/amqp-bunny](https://github.com/php-enqueue/enqueue-dev/blob/master/docs/transport/amqp_bunny.md)

而 Golang 的世界之一:

*   [streadway/amqp](https://github.com/streadway/amqp)

* * *

http://phpprofi.ru/blogs/post/88 的читать

* * *

en:[https://blog . forma-pro . com/PHP-amqp-clients-benchmark-them-all-8 a4 E6 ad B1 a6 b](https://blog.forma-pro.com/php-amqp-clients-benchmark-them-all-8a4e6adb1a6b)