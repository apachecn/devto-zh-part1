# REST APIs 简介

> 原文：<https://dev.to/bengreenberg/a-brief-introduction-to-rest-apis-172>

即使你不知道 REST 是什么意思，你已经在网络上和它互动了很长时间。现在让我们花些时间来探讨一下 REST 的含义以及什么是 REST API。REST 代表“代表性状态转移”。罗伊·菲尔丁在 2000 年的博士论文中向世界介绍了这一概念。REST 是管理客户机-服务器关系的一种方式，通过合并可重用组件来提高速度和性能。RESTful 架构的基本特征是什么:

1.  无状态:在交互之间，客户端数据不存储在服务器上，会话存储在客户端(通常在会话存储中)。
2.  客户机服务器:在前端(客户机)和后端(服务器)之间有一个关注点的分离。它们彼此独立运行，并且都是可替换的。
3.  缓存:来自服务器的数据可以缓存在客户端，这可以提高性能速度。

除了 REST 的这三个基本特性之外，还有一个组成基本 URL 的统一方法。这允许服务的标准化，这在引入 REST 之前是不存在的。例如，对`/dogs`的`GET`请求应该产生数据库中的所有狗，而对`/dogs/5`的`GET`请求将呈现 ID 为 5 的狗。类似地，REST 使用标准的方法如`GET`、`PUT`、`DELETE`和`POST`来执行动作。

因此，RESTful API 应该是这样的:a)无状态的，b)分离客户端-服务器之间的关注点，c)允许客户端缓存数据，以及 d)利用标准化的基本 URL 和标准化的方法来执行操作、添加或删除数据的动作。

此外，REST APIs 也以标准格式返回数据已经成为惯例。现在最流行的格式是 JSON (JavaScript 对象符号)。数据格式的标准化是朝着 web 上资源交互方式的统一迈出的又一步，它允许开发人员解决问题，而不是将时间花在基本架构的配置上。

当从 API 请求数据时，您可能会得到类似这样的结果:

```
{
    title: "This is JSON",
    content: [
        chapter: "1",
        page: "200",
        firstParagraph: "This is what JSON looks like when it is returned from an API."
    ],
    author: "Ben Greenberg",
    source: "dev.to"
} 
```

Enter fullscreen mode Exit fullscreen mode

这允许使用类似于`data.title`的东西轻松访问 JSON 中的数据，返回“这是 JSON”。

在哪里可以找到 RESTful APIs？到处都是！[推特](https://dev.twitter.com/twitterkit/android/access-rest-api)。[谷歌](https://developers.google.com/drive/v2/reference/)。[易趣](https://go.developer.ebay.com/api-documentation)。我们每天利用的许多流行服务都利用 RESTful 架构来提供 API 服务。现在你也可以。