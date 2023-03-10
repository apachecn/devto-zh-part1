# 现代静态站点生成

> 原文：<https://dev.to/kbariotis/modern-static-site-generation-3i15>

在这篇文章中，我将谈论静态站点生成器。它们是如何进化的，以及为什么我从一个幽灵站点切换到一个现代静态站点生成器 T2 站点。

## 我们所知道的静态站点发电机

[哲基尔](http://jekyllrb.com)，[赫索](https://hexo.io)，[雨果](https://gohugo.io/)。他们做一件事，而且做得很好。我已经用 Jekyll 在 GitHub 或其他地方建立了大量的站点。它们的工作方式都差不多:

*   你用一些通用的模板语言描述你的内容( [Pug](//pugjs.org) ，[handler](http://handlebarsjs.com)等等)
*   在开发过程中，启动一个本地 web 服务器并添加文件“观察者”,它将监听内容变化并重新呈现站点
*   最后，用静态 HTML 呈现整个站点并进行部署。生成器将组合您的文件并生成一个格式良好的 HTML 内容。

这里需要注意的是，您必须将客户端逻辑与后端逻辑分开。很像 Wordpress 参与的架构。所以，几乎没有什么变化。拥有 Wordpress 的缓存版本和用 Jekyll 生成的内容没有区别。

## 进入世界新秩序

[React.js](https://facebook.github.io/react/) ！噢耶！我们听说过！它主宰了网络，但这是有原因的。React.js 获得如此高的知名度有很多原因。我们将在这里讨论的许多技术可以很容易地应用于其他框架和库，但是 React.js 可以很好地使用它们。

有一个名为[服务器端渲染](https://facebook.github.io/react/docs/react-dom-server.html)的函数，您可以直接从 React.js 组件树中生成静态 HTML 内容。该 HTML 内容将包含所需的 React.js 特定注释，因此当相同的树被加载到客户机上时，在先前生成的内容之上，将确切地知道什么必须呈现，什么不应该呈现。从那里开始，客户端逻辑可以领先。一旦用户访问了一个站点，服务器将用静态 HTML 来响应。一旦它被加载到用户的浏览器中，并且用户在另一个路径中导航，客户端逻辑将会响应并呈现新的页面，而不是再次联系服务器。相同的代码库将用于后端，也用于前端。这改变了游戏规则。

让我们来看看新的名单:

*   描述 React.js 组件中的内容
*   在开发的时候，像老板一样写代码(热重装，模块化代码，webpack 插件等)...)
*   使用 React.js SSR API 生成要在后端提供的静态内容，以及要在访问者的浏览器上使用的前端资产

## 等等，什么？像那样吗？

是啊，我知道。我让它看起来如此简单。不是的。需要一个强大的抽象来跟踪跨组件的链接，解析以另一种格式编写的内容，比如用 Markdown 编写的博客文章，生成不会使客户端膨胀的代码，并高效地向用户提供内容。

令人欣慰的是，有几个项目采取了主动，到目前为止他们做得很好。在阅读本文时，该网站已经由 GitHub pages 提供服务，一旦您查看来源，就会看到由 [Gatsby.js](https://gatsbyjs.org) 生成的内容。

我跟踪这些项目已经有一段时间了。以下是我的清单:

*   [Gatsby.js](https://gatsbyjs.org)
*   [现象](https://phenomic.io)
*   [下一个](https://nextein.now.sh)

在我开始关注他们的时候，他们都处于非常早期的阶段，没有一个能像我想要的那样生成我的网站的内容，也没有一个能像我之前的网站那样生成内容，这样就不会丢失路径和某些功能。我想让生成的网站在用户体验方面与旧网站完全一样。一旦 Gatsby.js 达到主要版本 1，我就开始使用它，结果...，你就看着吧！

## 我到底在看什么？

首先，如果你浏览我的网站，你会注意到浏览器没有完全重新呈现网站。Gatsby 将为每个路由生成一个 JSON 文件，因此浏览器只能请求该文件，React.js 将只呈现适当的组件。(之前，我使用了一种叫做 [pjax](https://github.com/kbariotis/kostasbariotis.com__ghost-theme/blob/master/src/js/app.js#L11) 的技术来创造同样的效果。虽然看起来一样，但它更像黑客。)

第二，可以看一下[源代码](https://github.com/kbariotis/kostasbariotis.com)。虽然你需要一些关于盖茨比如何工作的知识，但让我给你一个这个网站是如何产生的感觉。你可以在 [`/src/pages`](https://github.com/kbariotis/kostasbariotis.com/tree/master/src/pages) 找到我所有的博文和这个网站的主页(`/`、`/about`、`/drafts`)。常见组件见 [`/src/components`](https://github.com/kbariotis/kostasbariotis.com/tree/master/src/components) 。在 [`/gatsby-node.js`](https://github.com/kbariotis/kostasbariotis.com/tree/master/gatsby-node.js) 你可以找到渲染站点的路线。首先，它使用查询所有`.md`文件的 GraphQL api 加载所有帖子。然后，它使用 [`/src/templates/blog-post.js`](https://github.com/kbariotis/kostasbariotis.com/tree/master/src/templates/blog-post.js) 模板为每个帖子创建一个页面，在此之前，它再次使用适当的模板文件为所有帖子和标签创建一个带有分页的页面。

在开发中，Gatsby 将在内存中生成所有这些内容，并启动一个开发服务器，我可以用它来预览我的站点。

您可以通过克隆[源代码](https://github.com/kbariotis/kostasbariotis.com)来自己尝试，在安装完依赖项后，点击:

*   `npm run develop`启动开发服务器
*   `npm run build`构建站点(完成后检查`/public`文件夹)

## HTML 缓存的替代方案

静态站点生成可以看作是缓存内容的另一种形式。想象一下，当你缓存一个 Wordpress 驱动的站点的主页时，你为每个访问者提供缓存的内容。但事实是，在很多情况下，根本不需要像 Wordpress 这样的系统。

我的个人网站包含一个博客帖子列表和关于我的静态信息，我偶尔会更新一下。评论被托管在 [Disqus](https://disqus.com) 上。完全不需要像 Wordpress 这样的系统，甚至不需要像我过去 3 年一直使用的 [Ghost](https://ghost.org) 这样的系统。虽然它一直很好地为我服务，但我真的厌倦了更新它，ssh-ing 到服务器，进行迁移和做这样一个堆栈所需的其他操作。虽然也有在云端托管的选择，但他们会得到相当多的报酬，这是他们做这项工作应得的。

但是我需要这样的系统吗？一点也不。虽然我们都知道 Wordpress 的管理界面对维护我们的内容有多重要，但我认为我们可以将后端和前端分开，并自动内置许多新功能。想象一下，不是让 [memcached](https://memcached.org/) 在你的 WordPress 站点前面缓存你的 HTML，而是每次你的数据库改变时触发一个钩子，它将使用 [Gatsby](https://www.gatsbyjs.org/packages/gatsby-source-wordpress/) 重新生成前端。是的，Gatsby 支持多个后端源来加载您的内容。与其像我一样把它们存储在你的 VCS 里，你还可以从你的 Wordpress 的 MySQL 数据库中加载它们。

总之，现代静电发电机将使我们能够:

*   提供静态内容，无需维护复杂的堆栈
*   在后端和前端之间保持清晰的分离
*   拥有很酷的特性，如客户端路由和开发时的热重装，开箱即用
*   重用在访问者的浏览器上生成后端内容的代码

我希望你能尝试一下盖茨比，并让我知道你的想法。