# 加快你的柴堆工人

> 原文：<https://dev.to/dawncold/speed-up-your-pyres-worker>

我们有一个异步工作的队列系统，它是由`Pyres`和`Redis`构建的。如果您的应用程序很小，pyres worker 在执行作业之前分叉一个新的进程，然后终止工作进程，worker 只是等待新的作业。

随着我们的应用程序越来越复杂，每一个新的工作进程都要花费大量的时间来加载，包必须被加载，包的依赖项也必须被加载，例如，工作进程在执行作业代码上只花了 1 秒钟，但它需要 10 秒钟来加载。

我们认为如果`worker process`可以加载它需要的所有东西，新进程不需要加载，因为新进程是由`worker process`分叉的，python 知道哪个模块被加载，如果你加载一个已加载的模块，它就返回，非常快。也许`Worker`类可以被扩展并实现`before_fork`方法来加载一些东西，加载的模块可用于新的分叉进程。

另一种方法是，如果`working process`可以长期存在，它在一段时间内不需要再次加载模块。还有一个`pyres_manager`，它创造了一个`manager`，`manager`创造了一些`minion`，`manager`把`minion`变成了`pool`。如果你想使用`pyres_manager`，不要指望`resweb`工作正常，它不能显示`worker`，因为它不知道`minion`。