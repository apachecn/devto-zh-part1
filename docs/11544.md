# 拦截 AJAX 请求

> 原文：<https://dev.to/adamkdean/intercept-ajax-requests-d8k>

今天只是一个片段，一篇关于如何像恐怖分子一样劫持 AJAX 请求的文章的再博客，作者是 [Daniel Huckstep](https://twitter.com/darkhelmetlive) 。

有一些人说不要修改原型，因为你永远不知道其他图书馆会如何使用它们，但也有一些人认为在 60 英里/小时的道路上以 30 英里/小时的速度行驶是一个好主意，所以不是每个人都认为是好主意，是好主意。

```
(function(open) {
  XMLHttpRequest.prototype.open = function(method, url, async, user, pass) {
    // do some magic
    open.call(this, method, url, async, user, pass);
  };
})(XMLHttpRequest.prototype.open); 
```

Enter fullscreen mode Exit fullscreen mode

这对于拦截 AJAX 请求非常有效。在我的例子中，是修改 AngularJS 请求的 URL。