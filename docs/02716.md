# Node.js way - DI vs 模块要求？！

> 原文：<https://dev.to/imthedeveloper/the-nodejs-way---di-vs-module-require-8aj>

# 依赖注入 vs 模块需求

自从拿起一个心爱的项目，翻遍我所有的旧 Javascript 项目来回忆这个东西到底是如何工作的，我发现了一个稍微更深的理解，这不可避免地导致了困惑、质疑和大量的阅读。

今天的主题是依赖注入与需求模块，如果我每天和其他开发人员一起工作，我很容易找到答案。唉，我不得不去各种远程资源，如 [Stackexchange](https://softwareengineering.stackexchange.com/questions/336767/module-requiring-vs-dependency-injection-in-javascript) 、 [Medium](https://medium.com/@Jeffijoe/dependency-injection-in-node-js-2016-edition-f2a88efdd427) 、 [RisingStack](https://blog.risingstack.com/dependency-injection-in-node-js/) 和可信赖的 [Google](https://www.google.co.uk/search?q=javascript+dependency+injection+vs+module+require&spell=1&sa=X&ved=0ahUKEwi9tYb5rMzXAhWC6RoKHeKZBLMQvwUIJSgA&biw=1384&bih=651) ，在那里我经常遇到过时的例子、10 年前的偏见和一些关于为什么创始人的问题从一开始就是错的漂亮的口水战。我来这里是为了从一个开放讨论的社区中寻求启示。

TLDR；从其他模块中获取依赖关系的首选机制是什么？

在我目前的项目中，我发现我的“app.js”主文件需要一堆模块，以便将依赖于这些顶级模块的底层功能编织在一起。通常情况下，这可能是(我已经大大简化了这一点):

```
const MQTTClient = require('./mqtt')
const Router = require('./router')
const client = MQTTClient .start('hostname')

const messageRouter = Router.init(client,{options})

client.on('message',messageRouter.handleMessage(topic, payload)) 
```

这意味着在路由器中，我总是可以访问从 MQTT 分配的客户机方法和属性。我发现许多帖子暗示这是好的，我发现许多帖子暗示这是坏的。不过，以下是我的最终印象:

*   这甚至可能不是 DI，对我来说，这感觉就像我只是传递一个函数，一个作为参数的对象。
*   Node/Javascript 将缓存所需的模块，因此在我的项目中重复使用它只是引用该缓存。
*   路由器内部的测试变得非常容易。我发现创建一个假的客户机对象可以更好地控制我的测试。
*   从功能上来说，如果 require MQTT 位于 Router.js 中而不是我的应用程序主文件中，这没有什么不同，但是到我的应用程序的“单一路径”似乎更容易理解。
*   我不喜欢看到许多“必需的”模块分散在我的项目中，但同时，在大型项目中，通过函数输入和输出进行回溯以找到注入的根源可能会变得非常耗时。
*   Javascript 不像其他语言那样严重依赖 DI。模块需求实际上是将对象引入所需范围的另一种方法。
*   似乎有很多 DI JS 库正在产生，事实上你可以看到这些注入一直在 Express、Angular、哈比神等中发生。
*   人们对使用工厂来代替评论很多。

想法和看法？