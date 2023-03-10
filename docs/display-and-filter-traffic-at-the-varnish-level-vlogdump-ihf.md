# 在清漆层显示和过滤流量:vlogdump

> 原文：<https://dev.to/cosimo/display-and-filter-traffic-at-the-varnish-level-vlogdump-ihf>

最近几个月没怎么写。原因是我一直在构建 Opera Discover 服务后端，这是我们几天前在 Opera mobile for Android 上发布的。

几周前，在 Discover 的第一次压力测试会议期间，我写了这个叫做`vlogdump`的小工具，Opera 允许我[把它放在 github](https://github.com/cosimo/vlogdump) 上。除了学习 [awk](http://en.wikipedia.org/wiki/AWK) :-)，主要目的是**显示和过滤进入你的清漆守护进程的流量。**。

`vlogdump`并不是要取代`varnishlog`,但是我知道有时候 varnishlog 会给我太多的输出来处理，特别是当我想确定一个客户或者一个请求的时候。我知道 varnish 3.0.x 附带的 varnishlog 在这方面要好得多，但我们使用的是 2.1.x，而那个版本的 varnishlog 没有那么强。

`vlogdump`比 varnishlog 更容易阅读，但同时它比`varnishncsa`或典型的`access.log`格式传达了更多的信息。

这里有一个输出的例子:

```
$ varnishlog | vlogdump -v only_misses=1
  172.22.0.15 => GET /assets/e85ed0a7b1b87120a0a2bfa025531c6733a48802 HTTP/1.0 MISS
            <= 200 OK 28.432 ms
  172.22.0.18 => GET /assets/5a9e9440c5c85e8dc5d65e03e15c95e390901fa7 HTTP/1.0 MISS
            <= 200 OK 36.905 ms
  172.22.0.18 => GET /icons/categories/te/icon32x32-technology.png HTTP/1.0 MISS
            <= 304 Not Modified 0.589 ms
  172.22.0.15 => GET /api/fetch/article-preview/?client=2&language=en-GB HTTP/1.1 MISS
            <= 301 MOVED PERMANENTLY 8.381 ms
  172.22.0.18 => GET /assets/c3830e95b717761005e26ce49ebab253e0ccb40b HTTP/1.0 MISS
            <= 200 OK 291.354 ms
  172.22.0.18 => GET /api/category?client=2&language=en-GB HTTP/1.1 MISS
            <= 200 OK 58.025 ms
  ... 
```

另一个有趣的例子。

**显示导致缓存命中的事务的请求和响应头，并且请求头(其中任何一个)与“Android”匹配** :

```
$ varnishlog | vlogdump -v show_req_headers=1 -v show_resp_headers=1 -v req_headers_match=Android -v only_hits=1
  83.149.37.122 => GET /api/category/?... HTTP/1.1 HIT
            <= 200 OK         0.088 ms
   req.http.Accept = application/json;version=1
   req.http.Accept-Encoding = gzip
   req.http.Host = ...opera.com
   req.http.Connection = Keep-Alive
   req.http.User-Agent = Mozilla/5.0 (Linux; Android 4.1.2; GT-N7100 Build/JZO54K) AppleWebKit/537.31 (KHTML, like Gecko) Chrome/26.0.1410.58 Mobile Safari/537.31 OPR/14.0.1074.57768

   beresp.http.Server = Apache
   beresp.http.Content-Encoding = gzip
   beresp.http.Content-Type = application/json
   beresp.http.Vary = Accept-Encoding, Origin
   beresp.http.Content-Length = 4217
   beresp.http.Date = Sat, 25 May 2013 07:59:53 GMT
   beresp.http.X-Varnish = 1611090407 1611007435
   beresp.http.Age = 267
   beresp.http.Via = 1.1 varnish
   beresp.http.Connection = keep-alive 
```

既然您渴望尝试一下:-)，您可以用几个命令来完成，并且假设您已经安装了正确的 awk:

```
wget -q -Ovlogdump https://raw.github.com/cosimo/vlogdump/master/vlogdump
varnishlog | ./vlogdump [options] 
```

[文档](https://github.com/cosimo/vlogdump/blob/master/README.pod)列出了所有可用的选项。

你可以做**更多有趣的事情**:

*   显示每个事务的请求或响应标题(`-v show_req_headers=1`、`-v show_resp_headers=1`)
*   仅显示慢于 200 毫秒的请求(`vlogdump -v only_slow=200`)
*   仅显示缓存未命中或命中(`-v only_hits=1`或`only_misses=1`)
*   仅显示 URL 与 regexp X 匹配的交易(`-v url_match='X'`或`-v url_match='!X'`表示负匹配)
*   仅显示 HTTP 状态代码为 X ( `-v only_status=X`)的交易
*   仅显示请求或响应标题与给定正则表达式(`-v req_headers_match=Blah`，`-v resp_headers_match=Error`)匹配的事务

您也可以将这些选项中的大多数组合在一起。当您对一小部分流量感兴趣时，这非常有用，但是**您希望看到整个进行中的事务**。

不过，有一个建议。这是我的第一次(最后一次？)重要的 awk 脚本:-)我知道它工作得很好，我正在使用它，但是由于它的工作方式，**我不会让它长时间运行**，因为它会慢慢消耗你的内存来跟踪所有的事务和客户。

如果您有反馈或问题，请随时在 github 上发表评论或给我发邮件。