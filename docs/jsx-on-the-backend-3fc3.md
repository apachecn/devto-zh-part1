# 后端的 JSX？！

> 原文：<https://dev.to/orkon/jsx-on-the-backend-3fc3>

嗨！

最近我有了一个想法，如何在创建 NodeJS 服务器时改善开发体验(特别是使用 express)。但首先我想解释一下我在准系统 express 应用中发现的一些问题:

1)缺少 API 文档。你需要学习/整合 Swagger 或 API Blueprint 来获得合适的 API 文档
2)缺乏自动输入/输出验证。对于 JavaScript，如果一切都正确输入，就有点难以控制了。您需要自己编写验证，或者使用一些验证器库或 JSON 模式验证器。
3)可发现性低。没有简单的方法来生成带有类型和验证的客户端代码。

我认为很酷的是用一种标准格式来定义 HTTP API 服务器的所有方面。这种格式应该是简单的，而不是全新的。所以我认为 JSX 在这方面非常出色。对于 JSX，典型的服务器如下所示:

```
<Service
  id="hello-world-service"
  port={3000}
  version="0.1.0"
  contact="orkon"
  tags={['node', 'service']}
  endpoint="http://dev.server.at.home"
>
  <Middleware use={require('./middleware/logger')} />
  <Group name="Hello" description="All routes related to saying Hello">
    <Route
      description={'Generates a message in the format "Hello :who!"'}
      path="/hello/:who"
      method="get"
      handler={require('./handlers/getHello')}>
      <Request>
        <PathParameter name="who" type="string" />
      </Request>
      <Response schema={require('./schemas/getHello.res.json')} />
    </Route>
  </Group>
</Service> 
```

Enter fullscreen mode Exit fullscreen mode

```
const server = getExpressServer(compile(__dirname + '/definition.jsx'));

server.listen(3000, () => {
  console.log('server started');
}); 
```

Enter fullscreen mode Exit fullscreen mode

我所看到的很酷的事情:

*   易于阅读(尤其是如果你熟悉 JSX/React 的话)
*   更容易组合不同的 API
*   将实现与文档联系起来
*   由于 JSON 模式，允许生成输入/输出验证器
*   允许生成正确的 API 文档
*   允许发现如何调用服务
*   隐藏底层框架(可以是 express，koa 等。)
*   在 JSX 后面，它只是普通的 JS 类/对象，所以 JSX 的使用是可选的

项目网址:[https://github.com/OrKoN/api-express](https://github.com/OrKoN/api-express)

你认为这是个好主意吗？