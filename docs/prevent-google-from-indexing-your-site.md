# 阻止谷歌索引你的网站

> 原文：<https://dev.to/michael/prevent-google-from-indexing-your-site>

虽然我目前正在开发一个客户的网站，他们网站的根有一个简单的即将到来的页面。我决定也建立一个子域，这样我就可以把它作为一个开发环境，也可以把它作为一个链接发送给客户，这样他们就可以看到一个接近的进度表示，并实际上与网站互动。

这方面的一个挑战是，虽然我希望根域和即将到来的页面被谷歌索引，但我不希望子域被索引，因为在网站完成后的某个时候，我可能会删除子域。

## `noindex`法

[根据谷歌](https://support.google.com/webmasters/answer/93710)的说法，包含一个内容值为`noindex`和名称值为`robots`的 meta 标签会导致谷歌机器人在下次抓取时将该页面从谷歌搜索结果中完全删除。

这就是网页头部的`noindex` meta 标签的样子。

```
<head>
  <meta name="robots" content="noindex">
  Your cool website
</head> 
```

meta 标签需要包含在你不想让 Googlebot 索引的每个页面中。如果您想完全阻止 bot，而不是告诉哪些单独的页面不要索引，您将需要使用`robots.txt`方法。

## robots.txt 方法

另一种方法是[阻止所有搜索引擎爬虫机器人索引你的网站](https://stackoverflow.com/a/390379/703220)。为此，您将创建一个`robots.txt`文件，并将其放在域的根目录下。此方法还假设您拥有服务器的文件上传权限。

[robots.txt](https://developers.google.com/search/reference/robots_txt) 的内容会是:

```
User-agent: *
Disallow: / 
```

它告诉所有爬行器不要爬行整个域。例如，如果我有一个`dev.example-url.com`的子域，而我只想阻止`dev`的子域，我会将`robots.txt`文件放在该子域的根目录下。

```
http://dev.example-url.com/robots.txt 
```

## 我两个都需要吗？

不，你只需要一个方法，但是记住使用`noindex`标签，你需要把它添加到你不希望被索引的每个页面，而`robots.txt`将指示爬虫不索引整个子域。

* * *

最初发布于[李名炀](https://michaelsoolee.com/prevent-google-index/)