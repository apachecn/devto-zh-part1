# 如何用基于 API 的 CMS 构建一个 SEO 友好的 React 博客

> 原文：<https://dev.to/rogerjin12/how-to-build-a-seo-friendly-react-blog-with-an-api-based-cms>

*想了解更多类似的内容，请在推特上关注 [ButterCMS，并订阅我们的](https://twitter.com/ButterCMS)[博客](https://buttercms.com/blog/)。*

在本教程中，我们将向您展示如何使用 React、 [Next.js](https://github.com/zeit/next.js/) 和 [ButterCMS](https://buttercms.com) 来构建一个基于 CMS 的博客。本教程的最终代码是 Github 上的[。](https://github.com/buttercms/react-cms-blog-with-next-js)

[Next.js](https://github.com/zeit/next.js/) 是一个构建通用 React webapps 的小框架。Next.js 内置了 Webpack 和 Babel。你可以在这里阅读更多关于 Next.js [背后的哲学。](https://zeit.co/blog/next)

ButterCMS 是一个托管的基于 API 的 CMS 和博客引擎，让你使用任何编程语言构建 CMS 驱动的应用，包括 [Ruby](https://buttercms.com/ruby-cms) 、 [Rails](https://buttercms.com/rails-cms) 、 [Node.js](https://buttercms.com/nodejs-cms) 、[。净](https://buttercms.com/asp-net-cms)、[蟒](https://buttercms.com/python-cms)、[凤](https://buttercms.com/phoenix-cms)、[姜戈](https://buttercms.com/django-cms)、[烧瓶](https://buttercms.com/flask-cms)、[反应](https://buttercms.com/react-cms)、[角](https://buttercms.com/angular-cms)、[围棋](https://buttercms.com/golang-cms)、 [PHP](https://buttercms.com/php-cms) 、[拉拉夫尔](https://buttercms.com/laravel-cms)、[仙丹](https://buttercms.com/elixir-cms)、[流星](https://buttercms.com/meteor-cms)。你可以认为 Butter 类似于 WordPress，除了你用你选择的语言建立你的网站，然后用 API 插入动态内容。

### 入门

为您的应用程序创建一个新目录，并添加 package.json 文件:

```
{
  "name": "react-blog"
} 
```

接下来，安装 Next.js，React。在撰写本文时，我们希望安装 Next.js，用于稍后使用的自定义路由:

```
npm install next@beta react react-dom --save 
```

然后将一个脚本添加到您的包中。

```
{
  "scripts": {
    "start": "next"
  }
} 
```

Next.js 将`./pages`目录中的每个 js 文件视为一个页面。让我们通过在项目中创建一个`./pages/index.js`来设置一个基本的主页:

```
export default () => (
  <div>My blog homepage</div>
) 
```

然后只需运行`npm run start`并转到`http://localhost:3000`。

然后创建一个`./pages/post.js`并在`http://localhost:3000/post`处检查它:

```
export default () => (
  <div>A blog post</div>
) 
```

### 从 ButterCMS 获取博客文章

首先安装 ButterCMS Node.js API 客户机并重启服务器:

```
npm install buttercms --save 
```

我们将在 index.js 中加载 ButterCMS 包，并设置一个获取和显示帖子的 React 组件:

```
import React from 'react'
import Link from 'next/link'
import Butter from 'buttercms'

const butter = Butter('de55d3f93789d4c5c26fb07445b680e8bca843bd')

export default class extends React.Component {
  static async getInitialProps({ query }) {
    let page = query.page || 1;

    const resp = await butter.post.list({page: page, page_size: 10})    
    return resp.data;
  }
  render() {
    const { next_page, previous_page } = this.props.meta;

    return (
      <div>
        {this.props.data.map((post) => {
          return (
            <div><a href={`/post/${post.slug}`}>{post.title}</a></div>
          )
        })}

        <br />

        <div>
          {previous_page && <Link href={`/?page=${previous_page}`}><a>Prev</a></Link>}

          {next_page && <Link href={`/?page=${next_page}`}><a>Next</a></Link>}
        </div>
      </div>
    )
  }
} 
```

With Next.js `getInitialProps`将在初始页面加载时在服务器上执行，然后在使用内置的`<Link>`组件导航到不同的路线时在客户端上执行。`getInitialProps`也接收具有各种属性的上下文对象——我们访问`query`属性来处理分页。我们正在从 ButterCMS 测试帐户获取帖子——使用 Github 登录来设置您自己的帖子。

在我们的`render()`方法中，我们使用了一些巧妙的语法，仅在适用时才显示分页链接。我们的帖子链接会将我们带到 404-我们接下来会让这些工作。

### 创建我们的帖子页面

为了让我们的文章链接工作，我们需要为我们的博客文章设置动态路由。首先，创建一个定制服务器`./server.js`，它将所有的`/post/:slug`URL 路由到我们的 post 组件:

```
const { createServer } = require('http')
const { parse } = require('url')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare().then(() => {
  createServer((req, res) => {
    // Be sure to pass `true` as the second argument to `url.parse`.
    // This tells it to parse the query portion of the URL.
    const parsedUrl = parse(req.url, true)
    const { pathname, query } = parsedUrl

    if (pathname.includes('/post/')) {
      const splitPath = pathname.split("/");

      // Add post slug to query object
      query.slug = splitPath[2];

      app.render(req, res, '/post', query)
    } else {
      handle(req, res, parsedUrl)
    }
  })
  .listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
}) 
```

我们还将更新我们的 post 组件，通过 slug 获取博客文章，并呈现标题和正文:

```
import React from 'react'
import Butter from 'buttercms'

const butter = Butter('de55d3f93789d4c5c26fb07445b680e8bca843bd')

export default class extends React.Component {
  static async getInitialProps({ query }) {
    const resp = await butter.post.retrieve(query.slug);  
    return resp.data;
  }
  render() {
    const post = this.props.data;

    return (
      <div>
        <h1>{post.title}</h1>
        <div dangerouslySetInnerHTML={{__html: post.body}} />
      </div>
    )
  }
} 
```

最后，更新我们的`package.json`启动脚本以使用我们的客户服务器并重启:

```
"scripts": {
  "start": "node server.js"
} 
```

### SEO

Next.js 提供了一个用于设置 HTML 标题和 meta 标签的`<Head>`组件。将`import Head from 'next/head'`添加到`./pages/post.js`的顶部，并使用`render()`方法中的组件:

```
render() {
  const post = this.props.data;

  return (
    <div>
      <Head>
        {post.seo_title}
        <meta name="description" content={post.meta_description} />
        <meta name="og:image" content={post.featured_image} />
      </Head>

      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{__html: post.body}} />
    </div>
  )
} 
```

重启服务器，检查一篇文章的 HTML 源代码，验证标签设置是否正确。

### 总结起来

Next.js 是一个强大的框架，可以轻松构建通用的 React 应用程序。有了 ButterCMS，你可以用 React 和 Node.js 快速构建 CMS 支持的博客和网站。

我们希望你喜欢这个教程。如果你有任何关于设置你的 ButterCMS 驱动的 Next.js/React 应用的问题，请通过 roger@buttercms.com 或推特联系我。