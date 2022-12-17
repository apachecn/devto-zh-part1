# 在 JavaScript 中从 URL 中移除参数

> 原文：<https://dev.to/adamkdean/remove-params-from-url-in-javascript-5l>

假设我们想要移除的参数是`session`，我们的 URL 是`http://www.example.com/?session=lasgfnasolgnasgn&id=500&other=100`。我们可以像这样删除它:

```
var oldUrl = "http://www.example.com/?session=lasgfnasolgnasgn&id=500&other=100";
var newUrl = oldUrl.replace(/&?session=([^&]$|[^&]*)/gi, "");
console.log(newUrl);

http://www.example.com/?id=500&other=100 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们还想从 URL 中删除 other，我们可以这样做:

```
var oldUrl = "http://www.example.com/?session=lasgfnasolgnasgn&id=500&other=100";
var newUrl = oldUrl.replace(/&?((session)|(other))=([^&]$|[^&]*)/gi, "");
console.log(newUrl);

http://www.example.com/?id=500 
```

Enter fullscreen mode Exit fullscreen mode

非常好。