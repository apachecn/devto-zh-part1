# Cloudflare 页面:失败:出现内部错误

> 原文：<https://dev.to/dz4k/cloudflare-pages-failed-an-internal-error-occurred-46il>

我在 Cloudflare 页面上部署我的网站(在撰写本文时)。我的构建失败，并显示错误消息:

```
17:24:49.795    Deploying your site to Cloudflare's global network...
17:24:50.632    Failed: an internal error occurred 
```

Enter fullscreen mode Exit fullscreen mode

没有其他信息。

谷歌搜索发现，很多事情都可能导致这个问题。在我的例子中，它是输出文件夹中一个名为`_redirects/`的**目录。**

Cloudflare 期望`_redirects`是一个从中读取重定向信息的文件。(事实上，这正是我使用它的目的——它被意外地输出到一个目录中)。