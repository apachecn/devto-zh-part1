# 如何用 MeteorJS 建立一个 CMS 驱动的博客

> 原文：<https://dev.to/rogerjin12/how-to-build-a-cms-powered-blog-with-meteorjs>

*想了解更多类似的内容，请在推特上关注 [ButterCMS，并订阅我们的](https://twitter.com/ButterCMS)[博客](https://buttercms.com/blog/)。*

你知道这个故事，你已经为你的客户建立了一个很棒的 MeteorJS 网站，他们想要一个位于子目录(不是子域)中的博客用于 SEO 目的。

在本教程中，我将向你展示如何使用 MeteorJS 和 ButterCMS 构建一个基于 CMS 的博客。本教程的完成代码是 Github 上的[。](https://github.com/buttercms/meteor-cms-blog)

[ButterCMS](https://buttercms.com) 是一个基于托管 API 的 CMS 和内容 API，让你使用任何编程语言构建 CMS 驱动的应用，包括 [Ruby](https://buttercms.com/ruby-cms) 、 [Rails](https://buttercms.com/rails-cms) 、 [Node.js](https://buttercms.com/nodejs-cms) 、[。净](https://buttercms.com/asp-net-cms)、[蟒](https://buttercms.com/python-cms)、[凤](https://buttercms.com/phoenix-cms)、[姜戈](https://buttercms.com/django-cms)、[烧瓶](https://buttercms.com/flask-cms)、[反应](https://buttercms.com/react-cms)、[角](https://buttercms.com/angular-cms)、[围棋](https://buttercms.com/golang-cms)、 [PHP](https://buttercms.com/php-cms) 、[拉拉夫尔](https://buttercms.com/laravel-cms)、[仙丹](https://buttercms.com/elixir-cms)、[流星](https://buttercms.com/meteor-cms)。Butter 允许你使用我们的仪表板管理内容，并通过我们的 API 将其集成到你选择的前端——你可以认为 Butter 类似于 WordPress，只是你用你选择的语言构建你的网站，然后使用 API 插入动态内容。

## 入门

如果你是 MeteorJS 的新手，查看他们的[快速入门指南](https://guide.meteor.com/#quickstart)或者按照下面的步骤操作。

安装 Meteor:

```
curl https://install.meteor.com/ | sh 
```

创建新应用并确保其运行:

```
meteor create meteor-cms-blog
cd meteor-cms-blog
meteor npm install
meteor 
```

打开网络浏览器，进入`http://localhost:3000`查看应用程序运行情况。

## 创建博客

首先安装 [ButterCMS Node.js API 客户端](https://github.com/buttercms/buttercms-node):

```
meteor npm install buttercms 
```

我们还将使用[铁路由器](https://github.com/iron-meteor/iron-router/)来设置我们的博客路线:

```
meteor add iron:router 
```

然后，我们将创建一些基本的路线和模板。我们对 ButterCMS 测试帐户使用 API 令牌。[登录 Github](https://buttercms.com/github/oauth) 创建您自己的帐户和 API 令牌。

`client/main.js`:

```
import Butter from 'buttercms';
import './main.html';

const butter = Butter('de55d3f93789d4c5c26fb07445b680e8bca843bd');

Router.route('/', function() {
  this.render("Home")
});

Router.route('/blog', function() {
  let that = this;

  butter.post.list({page: 1, page_size: 10}).then(function(response) {
    that.render('Blog', {data: {posts: response.data.data}});
  });
});

Router.route('/blog/:slug', function() {
  let slug = this.params.slug;
  let that = this;

  butter.post.retrieve(slug).then(function(response) {
    let post = response.data.data;

    that.render('Post', {data: {post: post}});
  });
}); 
```

`client/main.html`:

```
<head>
  Meteor Blog
</head>
<body>
</body>

<template name="home">
  <a href="/blog">View blog</a>
</template>

<template name="blog">
<h2>Blog Posts</h2>
{{#each posts}}
  <div>
    <a href="/blog/{{slug}}">{{title}}</a>
  </div>
{{/each}}
</template>

<template name="post">
  <h2>{{post.title}}</h2>
  {{{post.body}}}
</template> 
```

让我们仔细看看我们的一条路线，看看发生了什么。

```
Router.route('/blog/:slug', function() {
  let slug = this.params.slug;
  let that = this;

  butter.post.retrieve(slug).then(function(response) {
    let post = response.data.data;

    that.render('Post', {data: {post: post}});
  });
}); 
```

在上面的代码中，我们为 URL `/blog/:slug`创建了一个路由，它将 post slug 作为 URL 参数，然后使用该 slug 向 [ButterCMS](https://buttercms.com) 发出一个 API 请求，以获取 post 并呈现它。

## SEO

我们的博客已经设置好了，但是来自搜索引擎和社交网络的爬虫不执行 Javascript，所以我们的博客 SEO 很糟糕。

首先，我们将安装 [ms-seo 助手包](https://atmospherejs.com/manuelschoebel/ms-seo)，并确保我们有好的 HTML 标题、描述和元标签。

```
meteor add check
meteor add manuelschoebel:ms-seo 
```

ms-seo 提供了一个简单的`SEO.set`方法来配置标签。您可以通过检查 DOM 来验证标签设置是否正确。

```
Router.route('/blog/:slug', function() {
  let slug = this.params.slug;
  let that = this;

  butter.post.retrieve(slug).then(function(response) {
    let post = response.data.data;

    SEO.set({
      title: post.seo_title,
      meta: {
        description: post.meta_description
      },
      rel_author: 'https://www.google.com/+ButterCMS',
      og: {
        'title': post.seo_title,
        'description': post.meta_description,
        'image': post.featured_image
      }
    });

    that.render('Post', {data: {post: post}});
  });
}); 
```

最后，我们希望服务器渲染我们的博客，使其可被搜索引擎和 Twitter 等社交网络抓取。

最简单的方法是使用 [Meteor 的托管平台 Galaxy](https://www.meteor.com/galaxy/) ，它提供了集成的预渲染服务(Prerender.io)。Prerender.io 服务作为 Galaxy 的一部分免费提供。

遵循流星的[指南](https://guide.meteor.com/deployment.html#galaxy)部署到星系。要打开内置的 Prerender.io 集成，请添加 [Galaxy SEO](https://atmospherejs.com/mdg/seo) 包:

```
meteor add mdg:seo 
```

## 总结起来

如果你不想使用 Galaxy，可以手动集成 Prerender.io，另一个选择是在你的应用中实现服务器端渲染。在撰写本文时，服务器端渲染还没有得到 Meteor 的本机支持，但是您可以查看一下 SSR 支持的 [Meteor SSR](https://github.com/meteorhacks/meteor-ssr) 或 [Flow Router 的](https://github.com/kadirahq/flow-router/tree/ssr) alpha 版本。

Meteor 是一个强大的开发平台，它解决了为 web、移动和桌面构建 Javascript 应用程序的许多难题。然而，在 Meteor 中没有很多 CMS 选项可以用来构建 CMS 支持的功能，比如博客、常见问题解答和模板化页面。像 ButterCMS 这样的无头 CMS 可以让你在 Meteor 中轻松构建 CMS 驱动的应用程序。

我们希望你喜欢这个教程。如果你有任何关于设置你的 ButterCMS 驱动的流星应用程序的问题，请联系我在[roger@buttercms.com](mailto:roger@buttercms.com)！