# 这张图片也是一个 HTML 网页

> 原文：<https://dev.to/ben/this-image-is-also-an-html-webpage>

这是一张图片:

[![Also an HTML page](img/b50e1a66cb94ad76b36828bea778fe3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dmuuPpbC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://lcamtuf.coredump.cx/squirrel/)

但它也是一个 HTML 页面。以下是上图的标记:

```
<img src="http://lcamtuf.coredump.cx/squirrel/" /> 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试访问该网页:

[http://lcamtuf.coredump.cx/squirrel/](http://lcamtuf.coredump.cx/squirrel/)

很酷，是吧？

这不是我做的。我给你看是因为它很酷。

下面是对正在发生的事情的解释:

1.  该文件是一个有效的 jpeg 文件，元数据中包含一些 html。
2.  服务器用 Content-Type: text/html 响应，让浏览器将响应体解释为 html。
3.  浏览器忽略 jpeg 头，直到它看到并开始呈现 html。
4.  jpeg 元数据中的 html 以“
5.  html 有将文件呈现为图像的![](img/1463358c91a18247ec42dd8574599ce6.png)。

* * *

我发现这个黑客很吸引人，希望你也是。