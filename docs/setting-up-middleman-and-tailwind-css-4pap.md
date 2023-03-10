# 设置中间人和跟风 CSS

> 原文：<https://dev.to/vinneycavallo/setting-up-middleman-and-tailwind-css-4pap>

中间人和顺风使登陆页面变得轻而易举(明白吗？).以下是如何让他们一起工作！

总的来说，我是[中间人](https://middlemanapp.com/)静态站点生成器和静态站点/降价博客的忠实粉丝:

*   我的咨询公司的网站， [exnil.io](http://exnil.io) 是建立在中间人之上的。
*   我的个人博客是建立在[杰基尔](https://jekyllrb.com/)的基础上的——一种中间人的表亲。
*   你正在阅读的这个网站是 [Octopress](http://octopress.org/) ，一个已经不存在的哲基尔侄子。(曾经是 octo press…2018 年迁移到 Jekyll)

最近，我一直在兴高采烈地喝效用至上的 CSS kool-aid。正如在我之前的其他人也注意到的，[亚当·瓦森的博客文章](https://adamwathan.me/css-utility-classes-and-separation-of-concerns/)很好地论证了这一点，所以我不会在这里深入阐述我的理由。既然我刚刚提到了 Adam，那么我现在选择的以实用程序为中心的 CSS 框架是他共同创建的 [Tailwind CSS](https://tailwindcss.com/) (或者“*目前正在共同创建*)也就不足为奇了，因为他们目前正在努力向 1.0 迈进。真的什么时候任何一个软件的创作都可以处于*过去式*？)

我开始为一个新的 web 应用程序想法建立一个预发布登录页面，我正试图为这个想法把握社区脉搏。我暂时将它命名为 [Rate my Refactor](http://www.ratemyrefactor.com) ，它的目的是促进围绕重构和代码质量的具体实例的讨论和建议。

[![Rate my Refactor screenshot](img/3aefba075919af5b0dcd6a3ae510978f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DDOJjfl2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://vcavallo.github.img/rmr-screenshot.png)

由 Tailwind 和 middle 提供的速度和摩擦减少使他们的团队成为简单登录页面的自然选择。为了尽快投入运营，我去了谷歌，寻找一些让中间人、网络包、PostCSS 和 Tailwind 能够很好地合作的方向。(免责声明，我传统上是一名后端开发人员，所以`npm/yarn/webpack/gulp/js/postcss/etc/etc`的邪恶结合并不是唾手可得的。)

我在谷歌上找了很多关于这个的东西。可能是因为大多数前端开发人员从娘胎里出来就知道如何处理纱球、gulps 和 webby packs 但正如我所说，那不是我。显然我并不孤单:[http://www . impostor roster . com/posts/can-understand-how-web pack-works](http://www.impostorroster.com/posts/cannot-understand-how-webpack-works)。

所以我想一定还有其他人在寻找这个答案。

你好，有人！这是:

# 已经演示给我看怎么做了！

[这是为你准备的 GitHub 回购](https://github.com/vcavallo/middleman-and-tailwind)。这些是主要部分:

*   中间人设置:
    *   `config.rb`中的`activate :external_pipeline`行
*   Webpack 安装程序:
    *   `webpack.config.js`中的 js 和 css 设置
    *   `package.json`的内容
*   顺风配置:
    *   `postcss.config.js`
    *   `tailwind.js`(根据安装文件的指示生成)

作为一个额外的好处，这个 repo 还具有简单的 Heroku 部署所必需的配置。

# 最终产品…

我邀请你来看看[评价我的重构](http://www.ratemyrefactor.com)作为一个基本的中间人+顺风+Heroku 网站的例子。既然你正在阅读一个编程博客，你可能至少有点关心重构——那么为什么不在那里注册电子邮件列表呢？！

谢谢！如果您有任何改进或问题，请在下面的评论中提出。