# 用 Gatsby 发布 RSS 源

> 原文：<https://dev.to/mikewheaton/publishing-an-rss-feed-with-gatsby-a3m>

## 等等，RSS 是什么？

在他们关于[如何卷土重来](https://www.wired.com/story/rss-readers-feedly-inoreader-old-reader/)的文章中，《连线》解释道:

> 它的目标很简单:以一种标准化的格式跟踪给定网站内容的更新...它可以给你一个全面的，定期更新的你最喜欢的网站全天发布的所有内容。

这种标准化格式是一个 XML 文件(见本网站的一个文件)，它允许像 T2 这样的应用在你发布新内容时通知用户。

为你的 [Gatsby](https://www.gatsbyjs.org/) 博客生成这个相当简单。

## 发布订阅源

首先将 [gatsby-plugin-feed](https://www.npmjs.com/package/gatsby-plugin-feed) 包添加到您的站点。

```
npm install --save gatsby-plugin-feed 
```

Enter fullscreen mode Exit fullscreen mode

现在将它添加到`gatsby-config.js`中的插件数组中。

```
plugins: [
  // Other plugins will already be here, don't forget the comma.
  'gatsby-plugin-feed',
]; 
```

Enter fullscreen mode Exit fullscreen mode

RSS 源仅在生产模式下生成。你可以通过运行`gatsby build && gatsby serve`并导航到 [http://localhost:9000/](http://localhost:9000/) 来测试它。

恭喜你，你有 RSS 源了！🎉

或者也许你像我一样盯着一个丑陋的错误信息。让我们把它修好。

## 定制 feed

默认情况下，插件会寻找一个与每篇博文相关联的`slug`字段。这是代表文章的 URL 的一部分，例如 */blog/the-post-title* 。默认情况下，不会为每个帖子生成。

虽然你可以配置[另一个插件](https://www.gatsbyjs.org/packages/gatsby-plugin-slug/)来生成 slugs，但是你可以通过在每篇文章的前面包含一个`path`来完全控制你的 URL。

```
--------
path: '/blog/the-post-title'
--------

The content of your post. 
```

Enter fullscreen mode Exit fullscreen mode

接下来，更新`gatsby-config.js`,用包含您的定制配置的对象替换您之前添加的单行(使用默认选项)。

```
{
  resolve: `gatsby-plugin-feed`,
  options: {
    query: `
      {
        site {
          siteMetadata {
            siteUrl
          }
        }
      }
    `,
    feeds: [
      {
        serialize: ({ query: { site, allMarkdownRemark } }) => {
          return allMarkdownRemark.edges.map(edge => {
            return Object.assign({}, edge.node.frontmatter, {
              description: edge.node.excerpt,
              date: edge.node.frontmatter.date,
              url: site.siteMetadata.siteUrl + edge.node.frontmatter.path,
              guid: site.siteMetadata.siteUrl + edge.node.frontmatter.path,
              custom_elements: [{ 'content:encoded': edge.node.html }],
            });
          });
        },
        query: `
          {
            allMarkdownRemark(
              limit: 1000,
              sort: { order: DESC, fields: [frontmatter___date] }
            ) {
              edges {
                node {
                  excerpt
                  html
                  frontmatter {
                    date
                    path
                  }
                }
              }
            }
          }
        `,
        output: '/rss.xml',
        title: "RSS Feed",
      },
    ],
  },
}, 
```

Enter fullscreen mode Exit fullscreen mode

这并不像看起来那么复杂。您的 RSS 提要是由`serialize()`函数生成的。您将看到它返回一个帖子数组，每个帖子有一个对象，包含描述和发布日期等属性。这些值来自对网站元数据(顶部)和博客文章(底部)的 GraphQL 查询。

上面的代码很有可能对你有用。如果没有，使用 [GraphiQL](http://localhost:8000/___graphql) 工具来测试查询并探索可用的数据，直到能够完成每个帖子返回的对象。

虽然可能需要几次尝试来解决这些错误，但您很快就会拥有自己的博客配置了。这是一个*设置它，然后忘记它*的任务，当你的网站建立时，它会自动生成一个最新帖子的 RSS 源。

## 下一步

*   使用像 [Feedly](https://feedly.com/) 这样的应用程序订阅你的博客，随时关注你的 RSS 订阅。
*   探索[插件的其他配置选项](https://www.npmjs.com/package/rss#itemoptions)。
*   了解更多关于 [GraphQL 查询](https://graphql.org/learn/queries/)的信息。