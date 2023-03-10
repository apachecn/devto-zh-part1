# 命名事物:EventTarget 还是 EventEmitter？

> 原文：<https://dev.to/nektro/naming-things-eventtarget-or-eventemitter-4nmm>

> ![Jeff Atwood profile image](img/e0543a84701c92eceb30ce862e811da9.png)杰夫·阿特伍德@ codinghorr![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)计算机科学中有两个硬东西:缓存失效、事物命名和一个接一个的错误。2014 年 8 月 31 日上午 09:29[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=506010907021828096)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=506010907021828096)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=506010907021828096)

今天我们将解决其中一个挑战，命名事物。

不久前，在 JavaScript 领域，有一个许多开发人员都非常熟悉的现实，叫做“回调地狱”,随着`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)` s 的引入，这个现实得到了极大的帮助，但由于 JavaScript 的一个关键事实，它仍然存在。默认情况下，它运行在与页面 UI 相同的线程上，并且必须在渲染之间完成。因此，浏览器和 JavaScript 引擎有“事件循环”。

*   在 Web 上，我们有 [EventTarget](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget) 类。
*   而在 Node 中，我们有[event emitter]([https://nodejs . org/API/events . html # events _ class _ event emitter](https://nodejs.org/api/events.html#events_class_eventemitter))类。

就我个人而言，我喜欢`EventTarget`,因为它解释了这是一个可以成为事件目标的对象，无论该事件的来源是否来自它本身。它非常安静地显示出`dispatchEvent()`和`CustomEvent`的存在，而不言自明。

另一方面，我最近在 [HTTP 203 播客](https://developers.google.com/web/shows/http203/podcast/)上[杰克·阿奇博尔德](https://twitter.com/jaffathecake)说他更喜欢`EventEmitter`因为它显示了事件的来源。

如果有什么不同的话，网络是向后兼容的，但话语总是很有趣，可以启发和帮助未来，所以你的想法是什么？你认为哪个更能有效解释其目的？