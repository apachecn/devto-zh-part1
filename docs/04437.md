# 站点生成与了不起的盖茨比

> 原文：<https://dev.to/daviddeejjames/site-generating-with-the-great-gatsbyjs-68e>

当有人提到静态站点生成时，很多人会想到 Jekyll。我听说了一些关于它的好消息，并注意到它可以让我很容易地建立一个博客。我渴望学习一些新的东西，让一些东西运行起来，而不仅仅是我通常的 WordPress 主题。在某个[斯科特·托林斯基](https://www.youtube.com/user/LevelUpTuts)在[盖茨比](https://www.youtube.com/watch?v=b2H7fWhQcdE&feature=youtu.be)上发布视频之前，我正处于重建我的作品集网站的边缘。

GatsbyJS 是一个静态站点生成器，类似于 Jekyll，但是它是使用 [React](https://reactjs.org/) 编写的，并且允许你将你的页面作为 React 组件来编写！它有点类似于 create-react-app，其中几乎所有可怕的 Webpack 配置都已经从您这里抽象出来，并且已经准备就绪，因此您可以获得重要的东西，如...建设网站！(如果你是 React 的粉丝并且不相信，那么 [React 网站/文档](https://reactjs.org/)就是用 Gatsby 发布的！)

出于多种原因，这引起了我的注意。我想:

*   能够相对快速地建立一个新的博客网站
*   拥有一个加载速度快/性能好的网站
*   了解更多 React 和 JavaScript

我最终选择 Gatsby 的另一个原因是，它承诺您的项目可以连接到各种 API，甚至是您选择的 CMS。

## graph QL 和 Gatsby 插件的魔力

最初，在 Scott 和官方的 Gatsby 教程之后，我让它从好的 ol' Markdown 文件中提取我的内容。然后我看到了...好得令人难以置信，一提到把它连接到 [WordPress](https://wordpress.org/) ...我写 WordPress 主题已经快两年了，所以这让我非常兴奋。为了让我的后端作为 WordPress(包括 [ACF](https://www.advancedcustomfields.com/) )和前端作为 React，我找到了测试我的前端能力的完美地方。

一开始我持怀疑态度:我必须解析大量的 JSON 才能得到我需要的数据吗？我甚至从未与 WordPress REST API 交互过，我将如何查询它？

答案... [GraphQL](http://graphql.org/) 。Gatsby 自带这个工具，通过 npm 安装你选择的 [gatsby-source 插件](https://www.gatsbyjs.org/docs/plugins/)和一点点配置，你可以立刻开始查询。我惊讶于使用 GraphQL 的简单查询。你看着他们，然后说“啊，就这样？真的吗？”。Gatsby 甚至附带了一个浏览器内查询测试器，因此您可以确切地看到从 GraphQL 查询中获得了什么数据。想按日期排序那些博客文章吗？没问题，加个旗就行了。

## 总结与未来

仅仅几个周末，我就用我想要的博客重建了我的作品集网站。我强烈推荐 [Gatsby](https://www.gatsbyjs.org/tutorial/) 给任何开始熟悉 React 的人。在我开始这个项目之前，我不太了解:

*   静态站点生成
*   创建一个渐进式网络应用程序(PWA ),什么才算一个
*   反应路由器
*   GraphQL
*   Wordpress REST API

向前迈进，我想扩展该项目，包括在博客分页，使用[风格的组件](https://www.styled-components.com/)，并确保该网站在[灯塔](https://developers.google.com/web/tools/lighthouse/)(谷歌性能审计工具)评分 90+整体

TL；dr - Side 项目很棒，可以从中学到很多东西。保持在圈子里，倾听他人的声音，无论是看教程、听播客、在 twitter 上关注开发者、阅读 [dev.to](https://dev.to) 上的文章、博客等等。#永不停止学习

如果你像我一样，学习一些东西，建立一些东西，即使很小，然后写下来，然后你会有第一篇博文，就像这个:D