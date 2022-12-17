# 静态站点的基础

> 原文：<https://dev.to/rishabhs_dna/the-basics-of-static-sites-8d4>

对于大多数 web 开发新手来说，动态网站是 Web 2.0 交互中的标准。但情况并非总是如此。

# 移动到动态站点

当蒂姆·伯纳斯·李创建互联网时，作为科学家之间信息共享的门户，网页是静态的 HTML 文档。

当时，网站是这些静态文档的集合，完整存储，在服务器端只有很少的其他接口。随着通用网关接口的引入，这种情况发生了改变。CGI 允许网站运行脚本，这使得网络服务器不仅仅是静态 HTML 文档的存储设备。脚本的输出可以在服务器上运行，并显示在网站上。

这导致了动态网站——你可以与之互动和交流的网站。随着大量服务器端技术的出现，网站变得更像是在 web 服务器上提供的应用程序。

# 为什么要使用静态站点

然而，就速度、可靠性和可伸缩性而言，静态站点提供了无与伦比的性能。对于内容不经常改变的用例，比如博客、登录页面、文档和文件夹站点，静态站点是一个很好的解决方案。

一个静态网站完全按照它存储在网络服务器上的样子交付给用户。

这是因为静态网站完全存储在服务器中，其中只有静态的 HTML、CSS 和 JavaScript 文件。当服务器必须呈现静态站点时，它能够提供文件集合，而不必向后端数据库发送额外的查询。每次对站点内容进行编辑时，它都可以在服务器端的静态文件中进行更新。由于解释 HTML、CSS 和 JavaScript 的是浏览器，而不是服务器，这使得能够传送更安全的数据包，并减少网站故障的机会。

最常见的挑战是:

1.  与通过 WordPress、Drupal 或 Django 等内容管理系统发布相比，部署静态网站的技术难度更大。

2.  客户端可以提供的内容是有限的。例如，对于一个电子商务网站，静态路由不是一个好主意。同样，对于使用定向营销广告的网页盈利来说，静态网站将是一个死胡同(尽管你总是可以在静态网站上放置横幅广告)。

前者是一个减速带，但是像 Lektor 和 Publii 这样的工具(下面会详细介绍)使得降低进入门槛变得更加容易。Jekyll 是最流行的静态站点生成器之一，也越来越多地被采用。

静态的渐进式网络应用程序正在脱颖而出。这些支持大量使用 JavaScript 及其框架的客户端交互，这在早期可能仅限于动态站点。例如，查看 Balsamiq 的联系我们页面，[这里](https://balsamiq.com/company/contact/)。这是一个使用 React 的静态页面。

本文汇编了一个为自己的静态站点提供服务所需的资源列表。

注意:如果你在此之前没有静态网站的经验，不用担心。本文介绍了静态站点生成器和 CMSs，它们可以在这方面有所帮助。在评价静态网站的性能时，我真的提倡“眼见为实”的方法。所以只要继续前进，用这些工具中的一个跳进静态网站。

# 静止站点发电机

静态站点生成器的一个重要特征是它提供了一个简单的源文件夹，它本身就是一个独立的静态网站。在服务器上托管此文件夹将部署静态站点。这里是静态站点生成器的一个详尽的选项列表。

### 1。[杰基尔](https://jekyllrb.com/)

最流行的静态站点生成器之一，它支持从许多动态博客导入。它是用 Ruby 编写的，是 GitHub 页面背后的引擎。

### 2。 [Hexo](https://hexo.io/)

用 JavaScript 编写，由 Node.js 提供支持。它允许一个命令部署到 GitHub Pages、Heroku、OpenShift 等。

### 3。[铁匠](http://www.metalsmith.io/)

简单和一个非常可插入的静态站点生成器——除了核心功能，一切都由插件处理，所以它非常灵活。它是用 JavaScript 编写的，不仅仅可以用作静态站点生成器；无论是作为构建工具，甚至是文档生成器。

### 4。[雨果](http://gohugo.io/)

极其快速和灵活。这是用围棋写的。它带有一个简单的框架结构，以便您可以定制最小的细节。

### 5。[鹈鹕](https://blog.getpelican.com/)

Pelican 用 Python 编写，可以处理多种格式的内容输入(Markdown、reStructuredText 等。).它还提供英语以外的多语言支持。

### 6。[盖茨比](https://www.gatsbyjs.org/)

用 JavaScript 写的，生成静态渐进式的 Web App 一个优点是，几乎可以立即看到所做的每个更改，而不必每次都重新加载网页。它由 React.js 提供动力。

### 7。[温特史密斯](http://wintersmith.io/)

用 CoffeeScript 编写，构建在 Node.js 上。它很简洁，但是可以用插件定制和扩展。

### 8。 [Nuxt](https://nuxtjs.org/)

Nuxt 创建一个静态生成的 Vue.js 应用程序。它是用 JavaScript 编写的，使用 Vue SSR 来生成客户端和服务器端页面。

### 9。[中间人](https://middlemanapp.com/)

用红宝石写的。需要安装 Ruby 语言和 RubyGems 来运行中间人。缩小，压缩和缓存破坏是内置的。它遵循 Ruby on Rails 惯例。

### 10。[集合](http://assemble.io/)

用 JavaScript 写的，构建在 Node.js 上，它有很多可用的插件；许多选项可以定制。

### 11。[八月出版社](http://octopress.org/)

它建立在 Jekyll 框架之上，区别在于它消除了设置 Jekyll 站点的耗时部分。这是用红宝石写的。最新版本 Octopress 3.0 重新构建了框架，使每个功能都成为一个可扩展的宝石，拥有自己的文档。这样用户可以定制和选择 Octopress 中他们想要在 Jekyll 之上实现的部分。

### 12。[哈基尔](https://jaspervdj.be/hakyll/)

用 Haskell 语言写的。它非常适合中小型网站。

### 13。[杜父](https://sculpin.io/)

用 PHP 编写，设置非常简单。它将纯文本文件与 Twig 模板结合起来生成静态页面。

### 14。[古腾堡](https://www.getgutenberg.io/)

用 [Rust](https://www.rust-lang.org/en-US/) 写的，这赋予了它极大的编译速度。它是一个独立的可执行文件。Gutenberg 还内置了 Sass 编译和语法高亮功能。

### 15。[拼图](http://jigsaw.tighten.co/)

用 PHP 写的。Jigsaw 使用 Laravel 药剂来完成资产。可以预先配置 Elixir，以便在每次部署更改时自动重建站点。

### 16。 [Statik](https://getstatik.com/)

用 Python 写的。它提供了一个完全可定制的数据模型，能够定义定制的分类法。这对于创建博客之外的静态网站很有好处。

### 17。 [MkDocs](http://www.mkdocs.org/)

用 Python 写的。MkDocs 旨在用 Markdown 创建快速项目文档。它允许你在构建时进行预览，并且几乎可以在任何地方托管。

### 18。[仙人掌](https://github.com/eudicots/Cactus)

用 Python 写的。Cactus 很容易在本地开发并直接部署到亚马逊 S3。它可以选择从内置的开发网络服务器预览网站。

### 19。 [DocPad](https://docpad.org/)

用手稿写的。DocPad 是一个动态静态站点生成器；这意味着它从几个来源获取内容，并将它们呈现为静态输出。它有一个微小的核心，以插件的形式提供非必要的功能，这意味着它的精简和可扩展。

### 20。[现象](https://phenomic.io/)

用 JavaScript 写的。Phenomic 使用 React 框架，该框架呈现客户端和服务器端页面。用户看到的是站点的静态预呈现版本，当他们浏览新页面时，只提供呈现这些部分所需的数据。它现在正转向成为一个模块化的网站编译器，这样静态网站不仅可以在 React 中呈现，还可以在 React、Preact、Next.js、Vue 和 Angular 中呈现。

### 21。[反应-静止](https://github.com/nozzle/react-static)

用 JavaScript 编写，专注于网站性能、SEO 和用户/开发者体验。它将站点呈现为一个渐进的静态应用程序。

### 22。[竖琴](http://harpjs.com/)

Harp 是用 JavaScript 编写的，内置了预处理功能。它将项目编译成基本的静态资产。

### 23。[曝光](https://github.com/Jack000/Expose)

静态网站生成器，将图像和视频文件夹转化为照片信息。它作为 bash 脚本运行，需要 Imagmagik 和 FFmeg 才能工作。非常适合需要相册或图库的用户。这是一个关于 Expose 可以做什么的很好的例子。

### 24。[根源](http://roots.cx/)

用 CoffeeScript 写的。适用于构建静态前端，尤其是中小型前端。

### 25。[尼古拉](https://www.getnikola.com/)

用 Python 写的。它提供增量构建并支持多种输入格式——restructured text、Markdown、IPython 笔记本和 HTML。

### 26。[出拳](http://laktek.github.io/punch/)

Punch 用 JavaScript 编写，是一个为设计者和开发者构建的 web 发布框架。由于样板代码，它的设置很快，由于 Mustache，它的模板很少。

# 静态内容管理系统

你可能会问，如果我使用的是静态网站，为什么我需要 CMS？CMSs 确实简化了非技术用户的访问，并使内容瓶颈不会出现，因为只有一个推送内容的管道。大量静态站点生成器的问题是有大量的技术部署。这些 CMS 可以在这方面提供帮助。

### 1\. [Netlify CMS](https://www.netlifycms.org/)

开源；适用于大多数静态站点生成器；基于 Git 的。

### 2。 [Forestry.io](https://forestry.io/)

免费个人计划，付费计划从 9 美元/站点/月开始；支持哲基尔和雨果；基于 Git 的。

### 3。 [Siteleaf](https://www.siteleaf.com/)

免费开发者计划，付费计划起价 7.20 美元/月；支持 Jekyll 基于 Git 的。

### 4。[达图克斯](https://www.datocms.com/)

免费开发者计划，付费计划起价 9 欧元/月(超过一个站点有折扣)；支持者——杰基尔、雨果、中间人、金属匠、盖茨比、赫索；基于 API。

### 5。 [Publii](https://getpublii.com/)

开源；将网站发布为静态 HTML 文件；基于 App。

### 6。 [Sitecake](https://sitecake.com/)

开源；HTML 和 PHP 网站的拖放编辑器。

### 7。[云炮](https://cloudcannon.com/)

免费启动计划，付费计划从 25 美元/月开始；支持 Jekyll 基于 Git 的。

### 8 个。[讲师](https://www.getlektor.com/)

开源；将网站发布为静态 HTML 文件；基于 App 和 API。

### 9。[统觉](https://appernetic.io/)

免费计划到每周 14 次发布，付费计划从 0.5 美元到 20.5 美元；支持雨果；基于 Git 的。

### 10。[领结](https://bowtie.io/)

起价 10 美元/月的计划；默认情况下支持 Jekyll，但可以支持其他静态站点生成器；基于 Git 的。

### 11。[心满意足](https://www.contentful.com/)

免费开发者版，付费计划起价 249 美元/月；支持中间人，杰基尔，金属铁匠；基于 API。

静态网站的加载速度比普通网页快 6 倍！考虑到这样的数字和影响，不仅对用户体验，而且对谷歌排名和搜索引擎优化，静态网站肯定是一个值得考虑的领域。

如果你正在寻找建立自己的网站，那么这些文章可能也会有所帮助:

[我如何制作自己的网站](https://blog.zipboard.co/diy-website-tools-e4bda5b32934)

选择哪个 CMS 科比、格拉夫、哲基尔还是格拉夫

电子商务网站的最佳平台和内容管理系统

这篇文章最初发表在 [zipboard 博客](https://blog.zipboard.co/how-to-start-with-static-sites-807b8ddfecc)上。