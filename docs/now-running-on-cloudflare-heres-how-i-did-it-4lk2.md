# 现在运行在 CloudFlare 上！我是这样做的。

> 原文：<https://dev.to/seankilleen/now-running-on-cloudflare-heres-how-i-did-it-4lk2>

(这是一篇稍旧的文章，但我认为它可能对这里的人们有用。)

几个小时前，我的网站默认运行在`https`上，速度非常快。这都要感谢 [CloudFlare](https://www.cloudflare.com/) ，还有[我的](http://RyanKilleen.com) [哥哥](http://Github.com/RyKilleen) [Ryan](http://twitter.com/deathbypapercut) ，他们向我展示了在我们即将到来的项目中使用 CloudFlare 是多么容易。

## 我从这里得到了什么

这个网站已经在使用 Jekyll 的 Github 页面上运行了。因此，我从我的 markdown 内容中自动生成了一个非常快速的网站，并通过 Github 页面提供服务，它运行在 Github 自己的基础设施上。已经防弹了。

那么，为什么要引入这个额外的组件呢？

*   **完全 SSL 支持**。任何以前为网站配置过这个的人都知道这有多痛苦。因为主机的限制，我推迟了它。
*   **易页重写和规则**。CloudFlare 有大量的内置规则，比如将所有的`http`请求快速转换为`https`。
*   **将混合内容转换为 https** 。在通过`https`为一个站点提供服务后，通常其他内容(链接、CSS、JavaScript、图片)仍然通过`http`提供，这就带来了麻烦。CloudFlare 会动态重写这些内容，这在转换过程中非常有用。
*   **能够插入“应用程序”**。我使用谷歌分析，也在考虑 NewRelic。使用 CloudFlare，我可以通过一键设置来添加它们，CloudFlare 将确保这些组件始终出现在我的网站上。
*   **缓存**。Github Pages 已经被很好地缓存了，但是 Cloudflare 以其缓存能力和内置 CDN 而闻名。
*   对我来说是免费的。从“为什么不呢？”的角度来看，这很有帮助视角。

## 实现它的步骤

这些出奇的少&简单。

*   **注册 CloudFlare 帐户**。通过 CloudFlare 网站，这变得快速而简单。
*   **将我的站点添加到 CloudFlare** 。CloudFlare 会自动从我当前的站点获取 DNS 设置，因此当我切换到他们的服务时不会有任何痛苦。很棒的经历。
*   **将我的名称服务器转换为 CloudFlare 的名称服务器**。我用的是 Namecheap 的默认域名服务器，所以我用了他们的文档。
*   **等到 CloudFlare 说是活动的。**这没花多长时间——几分钟，肯定不到 30 分钟。
*   **将安全设置更改为“完全”。**这可以通过 CloudFlare 的控制面板轻松完成，并自动生成一个证书(该证书将自动更新)。
*   **等待证书激活**。这花了大约 15 分钟。
*   **测试是否可以通过`https`** 到达我的站点。它工作了，但是一些混合内容没有正确显示(如预期的那样)。
*   **打开重定向混合内容的设置**。这确保了所有的 CSS、JS 等。正确呈现，没有安全警告。
*   **通过手动转到`https`版本**来测试站点。这确保了一旦我执行它就不会有任何问题。
*   **创建页面规则来实施`https`** 。这很简单，只需为`http://*SeanKilleen.com*`添加一个规则，并将规则设置为总是强制 https。

然后…其实就是这样。在这几个小步骤之后，我完全完成了，并通过 CloudFlare 运行，默认情况下，我的站点由 https 提供服务。写这篇文章比进行转换花了更长的时间。

## 新时代

我不得不说，按照这些步骤，我惊讶地发现 CloudFlare 如此轻松地将其应用于他们的平台及其所有优势。我很熟悉像这样的转变会带来的痛苦，在整个过程中没有任何痛苦是令人惊讶和愉快的。

我对 CloudFlare 这样的平台感到非常兴奋，它们为更好的 web 铺平了道路。

## 问题？评论？

当你通过 https 加载时，这个网站的任何部分有没有给你带来不便？你对像这样的网站上的 CloudFlare 有什么想法吗？你是我哥哥来告诉我他有多正确吗？请在评论中发表意见！

[现在运行在 CloudFlare 上！我是这样做的。](https://SeanKilleen.com/2016/12/now-running-on-cloudflare/)最初由肖恩·基林于 2016 年 12 月 27 日在 SeanKilleen.com[发表。](https://SeanKilleen.com)