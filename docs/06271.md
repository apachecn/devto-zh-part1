# 我的 2017 堆栈

> 原文：<https://dev.to/atila/my-2017-stack-287g>

最近我开始重构我的网站，在过去 5 年的时间里，它已经达到了第 5 层。从 [wordpress](https://wordpress.com) 到 [jekyll](https://jekyllrb.com/) ，快速浏览先用 [Grunt](https://gruntjs.com/) 再用[gull](http://gulpjs.com/)编译的[双截棍](https://mozilla.github.io/nunjucks/)模板，我可以说经历了很多设置。既然开发人员拥有个人网站的全部目的是进行试验，所以我认为它已经达到了目的。

按照传统，是时候写下我当前的决定了，为什么与我之前的决定相比，我对这些决定感到高兴。

#### HTTPS

在任何免费计划中，通过 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 提供自定义域名服务都不是标准的:

*   [Gh-pages](https://pages.github.com/) 不支持
*   Heroku 只支持付费计划的 https
*   [激增](https://surge.sh/)也需要付费计划

有了 [Cloudfare](https://www.cloudflare.com/) 就有可能为 HTTPS 提供服务，此外，它还为您提供了一个创建子域名、管理您的 **DNS** 等的便捷仪表盘。

设置它是一个没有大脑，头在那里，并创建您的免费计划帐户。添加你的域名， [Cloudfare](https://www.cloudflare.com/) 会为你扫描域名，自动进行配置，然后为你提供分配给你的两个**域名服务器**。

然后，你去找你的登记员。添加**域名服务器**和 *bam* 。完成了。

一旦您的 **DNS** 传播完毕(可能需要一些时间，但对我来说需要几分钟)，您就可以查看**页面规则**设置，创建一个新规则(*强制 HTTPS* )并在过滤器上添加

```
http://\*.yourdomain.com\* 
```

你的流量将几乎立即开始被重定向到 HTTPS。

#### 上菜

一旦你成功地开始使用 [Cloudfare](https://www.cloudflare.com/) 作为你的子域的代理，你就可以开始考虑从哪里提供你的文件了。在我看来，我有 3 个选择可以考虑:

*   [Heroku](https://heroku.com)

我强调前端开发，这意味着我的游乐场(网站)不会有太多复杂的后端逻辑，如果有的话。所以，是的，我们不要通过过度设计来开始。

**编辑:**自从我写了这篇文章，我决定继续进行后端技术的实验，因此我重新评估了在 Heroku 上托管我的网站，以便我可以与 [NextJS](https://github.com/zeit/next.js) 一起玩，对我来说，最大的障碍是自由层让 *dyno* 在空闲时间休眠，使第一个请求花费了很长时间。由于我的网站没有流量来证明付费账户的合理性，休眠的 *dyno* 成为了一个交易破坏者

*   [Gh 页](https://pages.github.com/)

我一直通过 Gh-Pages 提供文件服务，它运行良好，很好，并且集中了你的关注。版本控制和服务在同一个地方。不过，自从我离开 Jekyll 之后，使用前端静态构建并没有想象中那么顺利。

将/dist 放在单独的分支上，然后将/src 放在单独的分支上是可能的，但是有点棘手，我知道的最好的方法是使用子树。不过话说回来，把它们分开可能/应该更简单。

*   [Surge.sh](https://surge.sh)

出于关注点分离的动机，我决定尝试只将版本控制留给 Github，并使用 Surge 来为我的静态服务。我不得不说:我对这个决定再高兴不过了。

我只是将我的/out 文件夹添加到。gitignore 文件，并在我的 npm 脚本上创建了一个部署任务。完成了。

另外，如果我愿意，我可以添加一个挂钩，并在 git push 之后立即运行部署任务。

Surge 非常好用，有一个简单易用的 CLI(命令行界面)。如果你的目标是服务一个静态生成的网站，我完全推荐你尝试一下。

如果你打算建立一个静态网站，我仍然建议你尝试一下 Surge。但是，因为我用的是 NextJS，所以我决定搬到[我们部署](https://wedeploy.com/)。现在，我的网站不再是静态的，有一些逻辑在后端工作，用于服务器端呈现 react 应用程序，这扩展了我的活动范围，允许我用一些节点进行测试。

#### 发展

在我的日常工作中，我一直在使用 [React](https://facebook.github.io/react) ，我对组件架构、 **css-in-js** 方法和所有这些东西都很满意。但是 SPA(单页应用程序)从来不适合我的个人网站。

NextJS 出现了，我立刻爱上了这个想法。**服务器端渲染**一个 [React](https://facebook.github.io/react) 应用是最近前端开发中发生的最好的事情之一。框架本身是伟大的，简约而强大。 [NextJS](https://github.com/zeit/next.js) 为你提供了一个易于定制的 NodeJS 服务器，一个简单的 CLI，[开箱即用的风格化 JSX](https://github.com/zeit/next.js#built-in-css-support) ，以及其他许多东西，从 3.0 版本起的一个静态网站生成器[。所以那是我选择的武器。](https://zeit.co/blog/next3-preview)

我已经用 [NextJS](https://github.com/zeit/next.js) 做了一些其他的工作，我不能推荐它。