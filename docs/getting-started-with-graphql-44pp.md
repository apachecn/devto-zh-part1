# Graphql 入门

> 原文：<https://dev.to/opeyemidjavu/getting-started-with-graphql-44pp>

## 简介

GraphQL 是今年最令人兴奋的技术之一，吸引了许多开发人员的注意力。GraphQL APIs 是 RESTful APIs 的一个可行的替代方案，它提供了更多的功能，可以轻松地在客户机和服务器之间读写关系数据。

GraphQL 生态系统中两个更令人兴奋的技术是 graphql-server 和 apollo-client，由 apollo 的人构建。在这篇文章中，我们将关注如何开始使用 GraphQL，启动基本服务器并使用 graphql-server 运行。

## 建筑

GraphQL 是一种应用层查询语言。在我们深入了解服务器的本质之前，让我们花一点时间来了解一些基本的 GraphQL 术语:

一个**查询**遍历相关对象及其字段，让客户机在一个请求中获取大量相关数据。
一个**变种**是针对 GraphQL 服务器的读写操作，它允许你添加和删除用户。
解析器**解析器**是一组特定于应用的函数，它们根据查询和变异操作与底层数据存储库进行交互
模式**模式**模式由一组对象类型构成，这些对象类型是命名字段的映射，每个命名字段都是另一种类型，或者是另一个对象，或者是标量基本类型。
一个**类型**定义了可以从 GraphQL 服务器返回的输出/响应数据的形状，包括作为其他类型边缘的字段。
输入**类似于类型，但是定义了发送到 GraphQL 服务器的输入数据的形状。
一个**标量**是系统实际数据所在的地方，它们是原语类型，比如字符串、Int、Boolean、Float 等。
**让我们开始吧****

这里我们将做 graphQL。Js，所以让我们从简单的“欢迎”服务器开始，这样我们就可以通过实现它来动手了。在开始之前，请确保您最好安装了 Node v6。打开命令行实用程序或终端，键入以下命令:

```
node -v
//to check your node version 
```

Enter fullscreen mode Exit fullscreen mode

如果您还没有配置 NodeJS，请转到 URL:[https://nodejs.org](https://nodejs.org)并按照说明安装它。
要创建一个新项目并在当前目录下安装 GraphQL.js，请执行:

```
npm init
npm install graphql –save 
```

Enter fullscreen mode Exit fullscreen mode

`--save`标志是将它作为一个依赖项添加到您的应用程序中，这样任何安装您的应用程序的人都会在启动 npm 安装时自动安装这些依赖项。

* *编写代码**

因此，为了处理 GraphQL 查询，我们需要一个定义查询类型的模式，并且我们需要一个 API 根，每个 API 端点都有一个名为“解析器”的函数。对于一个只返回“Welcome”的 API，我们可以把这段代码放在一个名为 server.js:
的文件中

```
var { graphql, buildSchema } = require('graphql');

// Construct a schema, using GraphQL schema language
var schema = buildSchema(`
  type Query {
    hello: String
  }
`);

// The root provides a resolver function for each API endpoint
var root = {
  hello: () => {
    return 'Hello world!';
      },
};

// Run the GraphQL query '{ hello }' and print out the response
graphql(schema, '{ hello }', root).then((response) => {
  console.log(response);
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后右击项目文件夹，点击打开终端并运行这个:
node server.js
然后你应该看到 GraphQL 响应打印出来:
`{ data: { hello: 'Welcome' } }`
更多信息请访问:[graphql.org](http://graphql.org)