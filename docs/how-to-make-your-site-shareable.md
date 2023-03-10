# 如何让你的网站可共享

> 原文：<https://dev.to/mogery/how-to-make-your-site-shareable>

使用开放图形标签，你可以确保你的网站在脸书、推特和其他社交网络上能被正确预览。

您可以使用 meta 标签来指定 HTML 头中的打开图形设置。

```
<meta property="og:type" content="website" />
<meta property="og:url" content="http://yoursite.com" />
<meta property="og:title" content="My Website" />
<meta property="og:image" content="http://www.navipedia.nimg/a/a9/Example.jpg" /> 
```

Enter fullscreen mode Exit fullscreen mode

这些标签足以让你的站点在分享链接的时候正确的上来。

你想要一个不同于网站的类型吗？[这里是所有打开的图形类型](http://ogp.me/#types)。

你的网站有脸书申请吗？完美！我们来补充一下。

```
<meta property="fb:app_id" content="0000000000000000" /> 
```

Enter fullscreen mode Exit fullscreen mode

你可以用这个开发工具在脸书上看到你被抓取的网站是什么样子。

你的网站有 Twitter 账号吗？我们也可以加上这个！

```
<meta name="twitter:site" content="@skiilaa"> 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们在 meta 标签中使用的是 name 属性，而不是 property 属性。

你可以用这个开发工具在 Twitter 上看到你被抓取的网站的样子。

除了默认的卡片样式，您还想要其他的吗？我们可以解决。

```
<meta name="twitter:card" content="summary_large_image"> 
```

Enter fullscreen mode Exit fullscreen mode

## 了解更多

*   [OpenGraph 协议](http://ogp.me/)
*   [脸书 OpenGraph 设置](https://developers.facebook.com/docs/sharing/opengraph)
*   [推特卡片](https://dev.twitter.com/cards/types)