# 新的圣杯？第一部分

> 原文：<https://dev.to/solisoft/arangodb--the-new-graal--part-1-6g8>

# ArangoDB:新的 Graal？第一部分

两年前我发现了 ArangoDB...我在寻找新的东西，新鲜的东西。我厌倦了为任何新项目部署新应用程序。然后我找到了 ArangoDB...而且我还奇怪为什么很少有人知道！

ArangoDB 是多模型数据库(key/value；文件；图表)以及事务和连接。

ArangoDB 提供了嵌入在 ArangoDB 中的微服务引擎 [Foxx](https://docs.arangodb.com/3.2/Manual/Foxx/AtAGlance.html) 。最后但同样重要的是，Foxx 服务可以随数据库集群扩展。它非常强大，可以覆盖你几乎所有项目的需求。

老实说，我现在在单实例模式下使用 ArangoDB。

服务看起来像什么？

```
'use strict';
const db = require('@arangodb').db;
const createRouter = require('@arangodb/foxx/router');
const router = createRouter();

module.context.use(router);

router.get('/', function(req, res) {
    res.send({ hello: 'world' });
}); 
```

Enter fullscreen mode Exit fullscreen mode

很简单，不是吗？您可以创建任何您想要的 Rest 动词(例如，`router.get`、`router.post`、`router.put`、`router.delete`、`router.patch`)

好的，那很酷...但是如何与数据库、收藏、图表互动呢？

ArangoDB 提供了一个名为 [AQL](https://docs.arangodb.com/3.2/AQL/index.html) 的语言请求，它允许你像 SQL 一样在数据库中运行请求。

假设我们已经创建了一个名为`posts`的集合

这里是一个端点，用于加载按特定状态过滤的所有帖子，并返回加入的用户。

```
router.get('/posts/:status', function(req, res) {
    const documents = db._query(`
        FOR post IN posts
            FILTER posts.status == @status
            FOR user IN users
                FILTER posts.user_key == user._key
                RETURN { post: post, user: user }
    `, { status: req.pathParams.status });

}) 
```

Enter fullscreen mode Exit fullscreen mode

ArangoDB UI 将为您提供一个漂亮的大摇大摆的文档，如:
[![swagger](img/ccce294d0309ff997d0e67cf6e79cd9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K5-U7lZR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.arangodb.com/wp-content/uploads/2012/02/arangodb_web_ui_foxx.png)

运行 Foxx 的最大好处是它位于数据库的中心，请求速度极快。

在第 2 部分的[中，我将向您展示如何使用](https://dev.to/solisoft/arangodb--the-new-graal--part-2-6mc) [Json Web 令牌](https://jwt.io/)来保护您的 API

对于在 OSX 工作的人，我写了 [foxx-framework](https://www.foxx-framework.com/) 来简化我的日常工作流程。