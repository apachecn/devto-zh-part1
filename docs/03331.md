# 为 Safari 和 Edge 创建 ImageBitmap 聚合填充

> 原文：<https://dev.to/nektro/createimagebitmap-polyfill-for-safari-and-edge-228>

克里斯·科伊尔，[css-tricks.com](https://css-tricks.com/)的开发者最近在推特上写道:

> ![Chris Coyier profile image](img/7518b2bf949206c97dd304820e17cda7.png)克里斯科伊尔[@克里斯科伊尔](https://dev.to/chriscoyier)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)写下你希望在谷歌搜索某样东西时找到的文章。2017 年 10 月 30 日下午 19:27[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=925081793576837120)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=925081793576837120)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=925081793576837120)

所以我正准备这么做。我最近一直在做一个网络应用游戏，我使用 Fetch API 动态地引入一些图像，然后将它们绘制到一个`<canvas>`上。我所做的事情的简化版本是这样的:

```
async function getPicture(url) {
    return fetch(url)
    .then(x => x.blob())
    .then(x => createImageBitmap(x));
} 
```

Enter fullscreen mode Exit fullscreen mode

这在 Chrome 上效果很好。出于某种原因其他地方没有。即使[图像位图](https://developer.mozilla.org/en-US/docs/Web/API/ImageBitmap)在[HTML 规范](https://html.spec.whatwg.org/multipage/webappapis.html#imagebitmap)中。

所以过了很长时间后，我想出了下面的 polyfill