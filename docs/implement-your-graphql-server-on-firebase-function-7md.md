# 在 Firebase 函数上实现您的 GraphQL 服务器

> 原文：<https://dev.to/mizchi/implement-your-graphql-server-on-firebase-function-7md>

## 为什么

SPA 和移动应用程序只需要一个单一端点来连接 graphql。

这种方式不能用 ws 后端实现订阅。我想我不需要它，因为 firestore 会解决它。

## 我不会在这篇文章里写什么...

*   如何部署到 firebase
*   GraphQL 服务器实现
*   web pack/NPM install/package . JSON

## 功能代码

functions/index.js

```
const functions = require('firebase-functions')
const express = require('express')
const { graphqlExpress } = require('apollo-server-express')
const bodyParser = require('body-parser')
const { makeExecutableSchema } = require('graphql-tools')

const schema = makeExecutableSchema({
  typeDefs: [/* Your schema.graphql */],
  resolvers: {/* Your resolver */}
})

const server = express().use(
  bodyParser.json(),
  graphqlExpress({ schema, context: {} })
)

exports.graphql = functions.https.onRequest(server) 
```

Enter fullscreen mode Exit fullscreen mode

## 向 graphql 发送请求

首先你需要`firebase deploy`

将 firebase.rules 写入代理 graphql 端点

```
{  "functions":  {  "source":  "functions"  },  "hosting":  {  "rewrites":  [  {  "source":  "/api/graphql",  "function":  "graphql"  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在本地运行`firebase serve`并通过 apollo 客户端请求查询。

```
import 'isomorphic-fetch'
import { ApolloClient } from 'apollo-client'
import { HttpLink } from 'apollo-link-http'
import { InMemoryCache } from 'apollo-cache-inmemory'
import gql from 'graphql-tag'

const client = new ApolloClient({
  link: new HttpLink({ uri: 'http://localhost:5000/api/graphql' }),
  cache: new InMemoryCache()
})

client.query({
  query: gql`<your query>`
}).then(ret => {
 console.log(ret)
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意:2017/11/27 我有一个关于 apollo-link 的问题，所以我直接将工作添加到 node_modules。[https://github.com/apollographql/apollo-link/issues/248](https://github.com/apollographql/apollo-link/issues/248)