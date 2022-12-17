# 拦截 XMLHttpRequest 请求

> 原文：<https://dev.to/adamkdean/intercepting-xmlhttprequest-requests-47lf>

让我们用一个非常有用的小片段开始我的代码博客的延续。我只是把它从代码库中移除，这样它就可以永远留在这里了。

```
(function(open) {
    XMLHttpRequest.prototype.open = function(method, url, async, user, pass) {
        // do some magic
        open.call(this, method, url, async, user, pass);
    };
})(XMLHttpRequest.prototype.open); 
```

Enter fullscreen mode Exit fullscreen mode

这将在请求发生之前拦截它，一旦您完成了您需要做的任何更改，无论是日志记录还是标记，您都可以继续它。