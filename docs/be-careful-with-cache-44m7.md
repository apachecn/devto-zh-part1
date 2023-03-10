# 小心缓存

> 原文：<https://dev.to/stipegrbic/be-careful-with-cache-44m7>

有一句众所周知的话是这样说的:在计算机科学中只有两件困难的事情:缓存失效和命名。这是一个关于第一个的帖子。

今天我遇到了一个缓存失效的问题，我看到的是缓存的数据，而不是新的数据。这使我无法解决一个只有我的同事才发现的问题，一时间我们对正在发生的事情感到困惑。

这只是提醒我下次在考虑缓存东西的时候要小心。知道何时使缓存失效是非常重要的。如果你没有正确地确定那个时刻，你将会在你的产品中放置一个讨厌的 bug，让你的用户感到沮丧。

*[https://martinfowler.com/bliki/TwoHardThings.html](https://martinfowler.com/bliki/TwoHardThings.html)