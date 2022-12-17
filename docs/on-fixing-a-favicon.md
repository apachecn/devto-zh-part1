# 修复一个网站图标

> 原文：<https://dev.to/philnash/on-fixing-a-favicon>

有时候开源工作只是修复一件让你烦恼的小事。然而，卷起袖子，钻研哪怕是最少量的代码，也可能会产生令人惊讶的结果。

我最近修复了一个图标。事情是这样的。

## 我给自己修了点东西

至少，解决这个问题让我更开心了。问题是[Crystal standard library documentation](https://crystal-lang.org/api/0.20.5/)没有显示 favicon，所以当我在浏览器中打开一堆标签时，我不知道哪些是我正在使用的 Crystal docs。

## 我为别人准备了一些东西

这是一个猜测，但如果我发现这是一个问题，那么我们可以假设至少有一个人在互联网上这样做。

## 我了解了更多关于图标的知识

这是我没想到的。我已经建立网站很长时间了，我想我知道所有关于图标的事情。我错了。

我一开始就犯了一个错误。我最初的[拉请求是针对 Crystal 项目本身的](https://github.com/crystal-lang/crystal/pull/3832)，并在文档的 HTML 布局的`<head>`中添加了`<link>`元素。这将使它依赖于水晶网站的结构。

反馈引导我再次尝试。这一次我想让水晶网站的 favicon 默认适用于 crystal-lang.org 域名上的任何页面，包括文档。这导致了一系列关于图标的发现。

*   如果你的`.png`和`.ico`图标都有一个`<link>`，那么 [Chrome 和 Safari 将总是选择`.ico`和](http://www.jonathantneal.com/blog/understand-the-favicon/)
*   火狐总是会选择`.png`
*   Internet Explorer 从版本 11 开始才支持`.png`图标

因此，如果你想使用一个高质量的`.png` favicon 和一个旧的 Internet Explorer 的后备，那么最好的做法是在`<head>`中包含一个`<link>`到`.png`图标，并将`.ico`版本放在默认位置:`/favicon.ico`。这样 Chrome、Safari、Firefox 和 IE11/Edge 都将选择`.png`图标，而旧的 Internet Explorer 将忽略`<link>`并寻找`/favicon.ico`。

当然，就像网络上的任何东西一样，[甚至比](https://github.com/audreyr/favicon-cheat-sheet)还要多。然而，这是[我产生的拉请求](https://github.com/crystal-lang/crystal-website/pull/15/files)，它被合并了。现在 favicons 在文档和主站点上工作。

## 我了解了更多关于水晶项目的情况

在我早期的 pull 请求中，我了解了 Crystal 标准库文档是如何生成和呈现的。然后，当我浏览 [Crystal 网站](https://github.com/crystal-lang/crystal-website)时，我发现它不仅当前是基于 [Jekyll](https://jekyllrb.com/) 构建的，而且还有一个[未解决的问题](https://github.com/crystal-lang/crystal-website/issues/12)指向我在[上的博客文章使用 Jekyll 资产构建资产管道](https://philna.sh/blog/2016/06/28/asset-pipelines-with-jekyll-assets/)。如果世界上有一件事是我绝对有能力做的，那就是用我自己的博客文章让缓存资产变得更好。于是我卷起袖子准备[另一个拉请求](https://github.com/crystal-lang/crystal-website/pull/16)。

现在，我更加熟悉 Crystal 网站及其背后的团队，他们对我的拉取请求进行了评论、审核和合并。

## 最简单的工作也能让你去任何地方

我只是想让 favicons 工作，但它导致了 3 个拉请求，一个关于 favicons 和浏览器的发现的兔子洞，以及写一篇关于它的博客帖子的动机。最终，我还是得到了我想要的东西:当我使用 Crystal 时，更多可识别的标签。

开源对很多人来说意义重大。对我来说，这是一种在我帮忙的同时学习的方式，不管任务看起来有多小。我很乐意你在 Twitter 上与我分享开源对你意味着什么。

* * *

*[关于搞定一个贫民窟](https://philna.sh/blog/2017/01/27/on-fixing-a-favicon/)最初发布于[philna . sh](https://philna.sh)2017 年 1 月 27 日。*