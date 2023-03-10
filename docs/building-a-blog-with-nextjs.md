# 用 Next.js 创建博客

> 原文：<https://dev.to/timber/building-a-blog-with-nextjs>

*原载于[timber . io](https://timber.io/blog/building-a-blog-with-next-js)T3】*

Next.js 是构建通用 React 应用程序的一个非常棒的新框架。简单地说，这意味着您可以使用 React 在服务器上呈现模板，并以您最习惯的方式呈现前端组件。这样做的好处有很多(共享组件、更快的渲染、优秀的工具)，但是要让所有这些都正常工作通常是一件痛苦的事情。

Next.js 使这个过程变得简单，随着 V3 的发布，我想我应该写一个博客来学习和演示它是如何工作的。在本教程中，我们将使用以下内容:

*   `next`(③。x)
*   `styled-components`(非凡的 css-in-js 解决方案)
*   `next-routes`(下一步用于表达路线的中间件)
*   `express`(为我们的页面提供服务，尽管您也可以进行静态导出)

我强烈推荐你在这里跟随 github repo[https://github.com/timberio/next-go/](https://github.com/timberio/next-go/)，因为为了简洁起见，省略了一些组件。

### 1。入门指南

设置起来很简单，你可以按照文档来做，但是要点是安装 next、react 和 react-dom，添加一个简单的构建脚本并创建你的索引文件。

`yarn add next@beta react react-dom --save`

将以下脚本添加到您的`package.json`

```
{  "scripts":  {  "dev":  "next",  "build":  "next build",  "start":  "next start"  }  } 
```

然后在根目录下的`pages`文件夹中创建一个`index.js`文件

```
// ./pages/index.js

export default () => (
  <div>Welcome to next.js!</div>
) 
```

然后你可以运行`yarn dev`，你应该在`localhost:3000`开始运行。默认情况下，热重装是内置的，如果你检查根目录下的`.next`文件夹，你可以看到。

### 2。添加一些风格

接下来，我们将配置[样式组件](https://github.com/styled-components/styled-components)来设计我们的博客。

首次运行`yarn add styled-components`。

然后在根目录下创建一个定制的`_document.js`文件，如下所示:

```
import Document, { Head, Main, NextScript } from 'next/document'
import { ServerStyleSheet } from 'styled-components'
import 'styles/global-styles';

export default class SiteDocument extends Document {
  render () {
    const sheet = new ServerStyleSheet()
    const main = sheet.collectStyles(<Main />)
    const styleTags = sheet.getStyleElement()
    return (
      <html>
        <Head>
          <meta charSet="utf-8" />
          <meta name="viewport" content="initial-scale=1.0, width=device-width" />
          {styleTags}
        </Head>
        <body>
          <div className="root">
            {main}
          </div>
          <NextScript />
        </body>
      </html>
    )
  }
} 
```

自定义`_document.js`允许我们覆盖默认的页面布局，并在 react 应用周围注入我们自己的样式和标记。

### 3。创建布局

现在，让我们创建一个所有博客视图都将使用的主布局，将以下内容放入`layouts/Main.js`:

```
/* layouts/Main.js */

import Head from 'next/head'
import Wrapper from './Wrapper'
import Nav from 'components/Nav'
import Footer from 'components/Footer'

export default ({ children, title = 'This is the default title' }) => (
  <Wrapper>
    <Head>
      { title }
    </Head>
    <header>
      <Nav />
    </header>

    <main>
      { children }
    </main>

    <Footer>
      Footer
    </Footer>
  </Wrapper>
) 
```

我们将使用这种布局来包装我们的页面，这可以覆盖`<Head>`标签并将内容呈现到`{ children }`块中。

### 4。渲染帖子

现在我们已经设置好了布局，让我们修改我们的`index.js`页面来利用它，并呈现一些帖子。

用以下内容更新`pages/index.js`:

```
import React from 'react'
import Layout from 'layouts/Main';
import { getPosts } from 'api/posts'
import { Link } from 'routes'

import Post from 'components/Post'

const IndexPage = ({ posts }) => (
  <Layout>
    <ul>
      {posts.map(p => (
        <Post key={p.title} post={p} />
      ))}
    </ul>
  </Layout>
)

IndexPage.getInitialProps = async ({ req }) => {
  const res = await getPosts()
  const json = await res.json()
  return { posts: json }
}

export default IndexPage 
```

这里的关键是我们的`IndexPage`组件上的`getInitialProps`,它获取该页面呈现所需的所有数据。当在`localhost:3000`直接访问这个页面时，Next 将在页面呈现之前获取数据。如果我们从另一个页面导航到这个页面，不会发生额外的页面重新加载，Next 的客户端路由将接管并在呈现组件之前为我们获取数据，这要感谢`Link`组件。您甚至可以添加`prefetch` [属性](https://github.com/zeit/next.js/tree/v3-beta/#prefetching-pages)来告诉 Next 预取该页面，以获得极快的页面加载速度。

现在我们将使用一些示例 json 并将 api 放在`api/posts/index.js`中:

```
import fetch from 'isomorphic-fetch'

export function getPosts() {
  return fetch('https://jsonplaceholder.typicode.com/posts')
}

export function getPost(slug) {
  return fetch(`https://jsonplaceholder.typicode.com/posts?title=${slug}`)
} 
```

并在`components/Post/index.js`中添加我们的`Post`组件:

```
import React from 'react'
import { Link } from 'routes'
import Wrapper from './Wrapper'

const PostItem = ({ post }) => (
  <Wrapper>
    <Link route='post' params={{ slug: post.title }}>
      <a>
        <h3>{post.title}</h3>
        <p>{post.body}</p>
      </a>
    </Link>
  </Wrapper>
)

export default PostItem 
```

当你重新加载页面时，你应该会看到一个由我们的索引页面呈现的帖子列表，就像这样(你可以在 github repo[https://github.com/timberio/next-go/](https://github.com/timberio/next-go/)中看到样式)。

[![Next.js Blog Preview](img/079d3109a55be13c41dc6ac4770236fd.png)T2】](//images.contentful.com/h6vh38q7qvzk/4ZnPINC8piYgC6cwaueWso/aaa5a31ed35217f0595270e14b3566c1/Screenshot_2017-06-05_18.04.59.png)

### 5。发布页面

现在我们已经有了一个帖子列表，让我们添加一个路由来查看每个帖子。在`pages/Post.js`中创建一个新页面，如下所示:

```
import React from 'react'
import Link from 'next/link'
import styled from 'styled-components'
import Layout from 'layouts/Main';
import { getPost } from 'api/posts'

const PostPage = ({ post }) => (
  <Layout>
    <h1>{post.title}</h1>
    <p>{post.body}</p>
  </Layout>
)

PostPage.getInitialProps = async ({ query }) => {
  const res = await getPost(query.slug)
  const json = await res.json()
  return { post: json[0] }
}

export default PostPage 
```

这个页面负责获取和呈现单个帖子，所以让我们添加一个路由来显示它。我们将使用`next-routes`进行一些很好的表达性路线定义，所以只需运行:

`yarn add next-routes`

并在根目录下添加一个`routes.js`文件夹，如下所示:

```
const nextRoutes = require('next-routes')
const routes = module.exports = nextRoutes()

routes.add('index', '/')
routes.add('post', '/blog/:slug') 
```

然后确保在`./server.js`中添加该中间件

```
const express = require('express')
const next = require('next')
const routes = require('./routes')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()
const handler = routes.getRequestHandler(app)

app.prepare()
.then(() => {
  const server = express()
  server.use(handler)

  server.get('*', (req, res) => {
    return handle(req, res)
  })

  server.listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
}) 
```

现在，我们在`pages/index.js`中的`<Link route='post' params={{ slug: post.title }}>`组件将使用适当的参数映射到该页面，如果您单击一个组件，您应该会看到如下内容:

[![Next.js Post](img/49872fe2991a380474f918b43f6f82b1.png)T2】](//images.contentful.com/h6vh38q7qvzk/74mFamDazCuewqAeYY8qYy/f16103179b5390a43be2ebc6d569f95c/Screenshot_2017-06-05_18.24.56.png)

就是这样！您可以轻松地在`api/posts/index.js`中加入自己的端点，从您选择的 API 或 CMS 中获取数据。

你可以在[https://next-go.now.sh/](https://next-go.now.sh/)看到工作演示，在[https://github.com/timberio/next-go](https://github.com/timberio/next-go)查看代码。您还可以在 https://learnnextjs.com/[了解更多关于 Next 的信息。](https://learnnextjs.com/)