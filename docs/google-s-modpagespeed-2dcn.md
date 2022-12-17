# 谷歌的 Mod_Pagespeed

> 原文：<https://dev.to/scottrobertson/google-s-modpagespeed-2dcn>

> mod_pagespeed 加速你的站点，减少页面加载时间。这个开源的 Apache HTTP 服务器模块自动将 web 性能最佳实践应用于页面和相关资产(CSS、JavaScript、图像),而无需修改现有的内容或工作流。

当我在设置我的新 VPS 时，我决定尝试一下 mod_pagespeed(很方便，它几天前才刚刚出测试版)。事实证明，它非常容易设置和使用。

首先，你需要在
下载正确的软件包

```
https://developers.google.com/speed/docs/mod_pagespeed/download 
```

Enter fullscreen mode Exit fullscreen mode

一旦你下载了它，它就像运行一样简单:

```
dpkg -i mod-pagespeed-*.deb
apt-get -f install
Or for CentOS you run
yum install at # if you do not already have 'at' installed
rpm -U mod-pagespeed-*.rpm 
```

Enter fullscreen mode Exit fullscreen mode

默认安装包括 CSS 和 js 文件的缩小，以及 CSS 文件的合并(js 也可以启用)。默认情况下，内联图像、CSS 和 js 也是启用的，这允许这些类型的文件包含在主 HTML 布局中，如果它们足够小，可以这样做的话。这将保存向服务器请求更多文件的请求。

除了默认值，我决定在配置文件中启用一些非默认选项:

```
/etc/apache2/mods-available/pagespeed.conf
The ones i decided to enable where the following:
ModPagespeedEnableFilters inline_preview_images # Load low quality images before loading high quality ones
ModPagespeedEnableFilters resize_images # Resize the images before serving them (These are then cached in Varnish)
ModPagespeedEnableFilters insert_image_dimensions # If any image does not have width, height set, then insert them
ModPagespeedEnableFilters combine_javascript # Combine javascript files into one file
ModPagespeedEnableFilters collapse_whitespace # Minify the HTML output of the page
ModPagespeedEnableFilters resize_mobile_images # Resize smaller images on mobile
ModPagespeedEnableFilters remove_comments # Remove comments from JS,CSS and HTML files. 
```

Enter fullscreen mode Exit fullscreen mode

有些还没有 100%准备好投入生产，但是它们的风险值相当低。

从最初的测试来看，它似乎减少了大约 1/3 的页面负载。这在一个小网站上可能不是很多，但在一个有很多图片的大网站上，这可能是一个巨大的带宽成本节省，也为用户带来了更好的体验。