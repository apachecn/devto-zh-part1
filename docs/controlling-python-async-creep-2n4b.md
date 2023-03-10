# 控制 Python 异步蔓延

> 原文：<https://dev.to/tryexceptpass/controlling-python-async-creep-2n4b>

不久前，Python 在基础语言中加入了正式的异步性。玩[asyncio](https://docs.python.org/3/library/asyncio.html)T0】和`coroutines`很有趣，它们是几乎并行执行的基本构造。但是当你开始更多地集成常规代码库时，你可能会发现事情会变得棘手。尤其是当您被迫与同步代码交互时。

当调用*一个可用的*函数时，情况变得复杂了。这样做需要一个`async`定义的代码块或协程。除了如果你的调用者必须是异步的，那么你也不能调用*它*，除非它的调用者是异步的。然后强制其调用者进入异步块，依此类推。这就是“异步蠕变”。

[读下去...](https://tryexceptpass.org/article/controlling-python-async-creep/)