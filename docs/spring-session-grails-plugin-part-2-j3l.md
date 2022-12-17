# 春季会议 Grails 插件(第 2 部分)

> 原文：<https://dev.to/jeetmp3/spring-session-grails-plugin-part-2-j3l>

[![Fork me on GitHub](img/8407139a163848650cee3c86b3c9e5ab.png)T2】](https://github.com/jeetmp3/spring-session)

在我之前的博客 中，我已经解释了 [**spring-session grails 插件**](https://grails.org/plugin/spring-session) 以及 Redis datastore。我们还介绍了 JSON 序列化，当您使用 mongo datastore 时，这几乎是相同的。在这篇博文中，我将解释如何使用 MongoDB 作为会话存储。

要更改数据存储，您需要在您的`Config.groovy`中添加一个属性。

```
springsession.sessionStore=SessionStore.MONGO 
```

这将把 MongoDB 设置为您的数据存储。默认情况下，它会尝试连接运行在**本地主机**端口 **27017** 上的 mongo。让我们看看一些配置属性及其默认值。

***注:*** *的一些共同性质在前面的块中解释过。这些对于 mongo 数据存储*也是一样的。

```
springsession.mongo.hostName="localhost" // MongoDB host server. Default is localhost.
springsession.mongo.port=27017 // MongoDB port. Default is 27017.
springsession.mongo.database="spring-session" // MongoDB database to store sessions. Default is spring-session.
springsession.mongo.username="" // MongoDB username. Default is "".
springsession.mongo.password="" // MongoDB password. Default is "".
springsession.mongo.collectionName="sessions" // Mongo collection name to store session data. Default is "sessions".
springsession.mongo.replicaSet=[[:] ] // MongoDB replica set if any. It includes list of maps [[hostName: 'localhost', port: 27017] ] Default is [[:]].
springsession.mongo.jackson.modules=[] // Jackson module class if any. Default is empty list 
```

***注:*** *MongoDB 是一个 NoSQL 和无模式数据库。所以您不需要为会话创建数据库和集合。它会在创建会话时自动创建。*

默认情况下，它使用 Java 序列化。要使用 JSON 连载请访问本系列的 [**第一博客**](https://dev.to/blogs/2016/12/15/Spring-Session-Grails-Plugin) 。前两步是一样的，但是在第三步<sup>第三步</sup>第**步*用 spring-session 插件*** 注册我的模块类，你将不得不使用 mongo 特有的`jackson.modules`属性。

```
springsession.mongo.jackson.modules = ['demo.SimpleModule'] 
```

伙计们，这就是我们的观点。在我的下一篇博客中，我将解释如何在 spring-session grails 插件中使用 JDBC 数据存储。