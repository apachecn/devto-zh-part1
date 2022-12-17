# Jekyll 短 Url

> 原文：<https://dev.to/wkmn/jekyll-short-url-2p0g>

Jekyll 短 Url 使用 Goo.gl 作为后端，是你的 404 页面上的一个小脚本。

**请注意，谷歌已经关闭了 GOO。GL**

## 如何

把这个放在你的 404 页上。

```
<script>if (window.location.href.indexOf("sh") > -1) {var shortCode = document.location.href.substring( document.location.href.lastIndexOf( '/' ) );window.location.href = "https://goo.gl" + shortCode; }</script> 
```

Enter fullscreen mode Exit fullscreen mode

要使用 Jekyll 短网址只需导航 toyourblog.com/sh/shorturl.

**举例:**【https://clarkhacks.com/sh/48KCGo】T2

* * *

[通过电子邮件回复](//mailto:arpitbatra123@gmail.com?subject=Reply:%20Jekyll%20Short%20Url)