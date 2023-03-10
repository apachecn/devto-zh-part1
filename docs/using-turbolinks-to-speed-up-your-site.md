# 使用 Turbolinks 加速你的站点

> 原文：<https://dev.to/sunnysingh/using-turbolinks-to-speed-up-your-site>

*最初发布于[Ninjality 博客](https://ninjality.com/blog/turbolinks-site-speed)。*

在一个由浏览器呈现的单页应用程序(spa)的世界里，很难考虑用其他方式构建；您将获得很多好处，比如几乎即时加载页面的性能。然而，仍然有很好的用例来构建传统的服务器端网站，比如那些由内容管理系统(CMS)驱动的网站，比如 WordPress。在 Ninjality，[我们最近重新设计了](https://ninjality.com/blog/redesign-2017)，并选择使用十月 CMS 来帮助管理内容。选择 CMS 并不意味着你必须放弃 SPAs 的好处，所以我想展示一下我们是如何使用 [Turbolinks 库](https://github.com/turbolinks/turbolinks)来让我们的网站感觉像是建立在一个现代 JavaScript 框架中。

## 了解 PJAX

为了更好地理解 Turbolinks 的功能，了解一下已经存在多年的 PJAX 的概念是很重要的。PJAX 代表 pushState + Ajax，使用这两种技术来拦截链接点击，以便 JavaScript 能够通过 Ajax 更新 URL 和加载内容。最初，Turbolinks 和 PJAX 都需要后端集成，因此 Ajax 响应只包含页面标题和内容。虽然这对于性能来说仍然是理想的，但 Turbolinks 作为一个纯 JavaScript 库，只是通过手动解析完整 HTML 中的标题和内容来开箱即用。如果你是 Turbolinks 的新手，一个有趣的事实是，它曾经是一个只支持 Rails 的 Ruby gem，PJAX 有一个依赖于 jQuery 的 JS 库。现在，Turbolinks 是一个独立的库，可以集成到任何网站中。

## 安装涡轮增压器

您只需要将 Turbolinks JS 库包含到您的页面中。[查看自述文件](https://github.com/turbolinks/turbolinks#installation)获取说明，但是让我们假设您正在使用某种形式的 npm 捆绑器:

```
npm install turbolinks --save 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要导入并初始化 Turbolinks:

```
var Turbolinks = require('turbolinks')

Turbolinks.start() 
```

Enter fullscreen mode Exit fullscreen mode

如果你的网站非常简单，那么你已经完成了。大多数时候你需要一些额外的设置。

## 整合你的网站

如果您的站点上已经运行了一些 JavaScript，那么您很可能在监听页面加载或 domready 事件。在 Ninjality 的例子中，我们有代码高亮、平滑滚动、图像缩放、共享按钮和许多其他通常看起来像这样的库:

```
document.addEventListener('DOMContentLoaded', function () {
  // initialize scripts....
}) 
```

Enter fullscreen mode Exit fullscreen mode

然而，现在页面可以通过 Ajax 加载，我们需要另一个事件来知道这些页面何时加载，否则我们的脚本只会触发一次(当用户第一次手动访问站点时)。谢天谢地，Turbolinks 提供了这样一个活动:

```
document.addEventListener('turbolinks:load', function () {
  // initialize scripts....
}) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，从`DOMContentLoaded`到`turbolinks:load`的简单改变将修复您的脚本。嗯，大部分都是。让我们在下一节解决一些常见的 gotchyas。

## 一些常见的痛风

你最有可能使用 Google Analytics、Disqus、social widgets 和其他通过内嵌`<script>`片段加载的第三方工具。如何将这些转移到`turbolinks:load`事件中？

你需要一个动态脚本加载器。加载脚本库是我们在 Ninjality 上使用的。照例通过 npm 安装:

```
npm install load-script --save 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以再次使用谷歌分析:

```
var Turbolinks = require('turbolinks')
var loadScript = require('load-script')

Turbolinks.start()

document.addEventListener('turbolinks:load', function () {
  // initialize scripts...

  // Google Analytics
  if (typeof ga === 'undefined') {
    loadScript('https://www.google-analytics.com/analytics.js', function () {
      window.ga = window.ga || function(){(ga.q=ga.q||[]).push(arguments)};ga.l=+new Date
      ga('create', 'YOUR-TRACKER-ID', 'auto')
      ga('send', 'pageview')
    })
  } else {
    ga('send', 'pageview')
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

因为我们整合了脸书的意见，所以这里也要做同样的事情:

```
if (typeof FB === 'undefined') {
  loadScript('https://connect.facebook.net/en_US/sdk.js#xfbml=1&version=v2.8&appId=YOUR-FB-APP-ID')
} else {
  FB.XFBML.parse()
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我们不得不采取额外的步骤让脸书评论正常工作。这意味着在`<div id="fb-root">` :
中包含`data-turbolinks-permanent`属性

```
<div id="fb-root" data-turbolinks-permanent></div> 
```

Enter fullscreen mode Exit fullscreen mode

最后，您需要小心的最后一个 gotchya 是在`<body>`中包含脚本时。确保您添加了`data-turbolinks-eval="false"`属性:

```
<script src="bundle.js" data-turbolinks-eval="false"></script> 
```

Enter fullscreen mode Exit fullscreen mode

虽然 Turbolinks 声称可以正确解析内联脚本，但我们的 JavaScript 在页面导航期间多次运行时会出现问题。

## 关闭思绪

对于许多需要管理功能的基于内容的网站来说，像 React 这样的前端框架是多余的。十月 CMS 允许我们有一个简单的搜索引擎优化和管理界面的设置。通过集成 Turbolinks，我们还能够在服务器呈现的网站和单页应用程序的优势之间进行折衷。请记住，Turbolinks 不能代替构建 SPA，因为使用 Turbolinks 无法获得其他好处，如状态管理或快速 UI 开发。如果这篇文章帮助您将 Turbolinks 集成到一个项目中，请发表评论或发推文@NinjalityDesign ！