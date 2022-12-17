# 在 cPanel 服务器上安装 Varnish 缓存

> 原文：<https://dev.to/scottrobertson/install-varnish-cache-on-cpanel-server-118e>

我在 cPanel VPS 上安装[清漆缓存](http://scottymeuk.in/Joxt)已经有一段时间了。主要原因是 cPanel 处理 Apache vhost 文件的方式。对于每个域，它把它们都分离到不同的文件中，当你需要编辑它们时，这使得事情变得非常困难。

最近，我找到了一个解决方案，那就是名为 ApacheBooster 的插件。它消除了在 cPanel 服务器上安装 Varnish 的痛苦，并使安装变得非常快。

这帮助我大大减少了图像加载的时间，也意味着我的 VPS 有更多的免费资源用于 PHP 等。

要在 cPanel 服务器上安装 ApacheBooster，请执行以下操作:

```
wget http://prajith.in/downloads/apachebooster.tar.gz
tar -zxf apachebooster.tar.gz
cd apachebooster
sh install.sh 
```

Enter fullscreen mode Exit fullscreen mode

如果你有任何疑问或问题，请在下面的评论中告诉我。