# 普罗米修斯，蟒蛇，烧瓶

> 原文：<https://dev.to/gimlet2/prometheus-python-flask-4ik0>

基于 Python 的应用程序是我们公司生态系统的重要组成部分。在微服务时代，实际上使用哪种技术没有区别。但是每个服务都应该提供自己的 API 和监控其状态的方法。

在我们的设置中，我们使用 [**普罗米修斯**](https://prometheus.io/) 来聚合所有服务的指标。而且效果很好。尤其是我们的 **JVM** 应用。但直到今天，这并不是所有蟒蛇的情况。

作为一个历史传统，我们的 Pythons 应用程序大多是用 [**烧瓶**](http://flask.pocoo.org/) 完成的。这是一个很好的框架。在某些情况下，它还包裹着 [**Gunicorn**](http://gunicorn.org/) 。这里有一个如何让它与普罗米修斯一起工作的教程:

1.  将依赖项添加到您的项目**setup . py**to Prometheus _ client = = 0 . 1 . 1
2.  在您的应用程序中定义一些您想要收集的指标，例如:FLASK _ REQUEST _ LATENCY = Histogram(_ _ name _ _。替换(' . ')，' _') + '_request_latency_seconds '，' Flask Request Latency ')。
3.  用@FLASK_REQUEST_LATENCY.time()注释您想要测量的方法
4.  添加端点以公开统计信息: