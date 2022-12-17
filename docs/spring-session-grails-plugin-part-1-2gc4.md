# 春季会议 Grails 插件(第 1 部分)

> 原文：<https://dev.to/jeetmp3/spring-session-grails-plugin-part-1-2gc4>

[![Fork me on GitHub](img/8407139a163848650cee3c86b3c9e5ab.png)T2】](https://github.com/jeetmp3/spring-session)

本教程将指导您如何在 grails 2x 应用程序中配置和使用 [**spring-session grails 插件**](https://grails.org/plugin/spring-session) 。本博客还将介绍该插件目前支持的各种数据存储，以及序列化器，以便将您的会话序列化/反序列化到存储中。

这个博客分为三个部分

1.  介绍
2.  装置
3.  Redis 数据存储区

#### 1。介绍

这个 **grails 插件**帮助您在 grails 应用程序中轻松设置 [**Spring Session**](http://projects.spring.io/spring-session/) 项目。

##### 什么是春会？

Spring Session 是 Spring 社区的一个项目，它提供了一个 API 和实现来管理用户的会话信息。它允许您将用户的会话存储在数据存储中，如 **Redis** 、 **MongoDB** 、 **JDBC** 、 **Hazelcast** 、 **Gemfire** 等。您可以轻松地从一个数据存储转换到另一个数据存储。

它如何优于存储用户会话的传统方式？在 Spring session 中，你不需要在 Servlet 容器中添加任何类型的 jar(Tomcat、jetty 等)，这意味着没有特定于容器的依赖性。与会话管理相关的 API (jar)将位于您的应用程序本身内部。因此，一个容器可以包含多个应用程序，这些应用程序可能使用不同类型的数据存储来存储用户的会话。这也有助于从一个容器迁移到另一个容器或转换到另一个供应商(即 Tomcat 到 Jetty)。

spring-session 提供的其他功能:

*   **集群会话** -支持集群会话而不依赖于应用程序容器特定的解决方案是很容易的。
*   **多个浏览器会话**——Spring Session 支持在单个浏览器实例中管理多个用户的会话(即类似 Google 的多个认证账户)。
*   RESTful API-Spring Session 允许在头文件中提供会话 id 来使用 RESTful API。
*   **WebSocket 支持**——提供在接收 WebSocket 消息时保持 HttpSession 活动的能力。

通过使用这个插件，您可以在 grails 应用程序中实现上述特性。

**注意:** Spring Session 项目支持各种数据存储，这在这个插件中还没有配置。这些数据存储正在开发中。如果你希望在增加数据存储支持方面有所贡献，只需[在 GitHub](https://github.com/jeetmp3/spring-session) 上帮我。

#### 2。装置

插件配置非常容易。如果您使用 grails 2x，那么在`plugins`块
内的`BuildConfig.groovy`中添加以下代码

```
plugins {
    ...
    compile "org.grails.plugins:spring-session:2.0.0-RC1"
} 
```

**注意:** *默认情况下，插件将使用 Redis 作为它的默认数据存储。目前，它支持 Redis、Mongo 和 JDBC 存储来持久化会话。*

目前，这个插件支持 3 个数据存储。

1.  雷迪斯
2.  蒙戈
3.  JDBC

本博客将涵盖 **Redis** 数据存储。其他数据存储将在下一篇博客中讨论。

##### 1。再说一遍

最初，spring-session 项目只支持 Redis 数据存储。所以 Redis 是这个插件的默认数据存储。要更改数据存储，您需要在您的`Config.groovy`文件中设置以下属性。

```
springsession.sessionStore=SessionStore.REDIS 
```

确保 Redis 服务器正在您的系统上运行。要验证插件是否工作，只需在你的应用程序中创建一个虚拟控制器。假设您已经创建了一个名为 HomeController 的控制器。

```
package demo

class HomeController {

    def index() {
        render "Hello from home controller"
    }
} 
```

现在运行 grails 应用程序，一旦应用程序启动，就在终端上运行下面的命令。

```
curl -c - localhost:8080/g2ss/home/index

#Output
#HttpOnly_localhost FALSE /g2ss/ FALSE 0 SESSION 9c3796b4-90d1-4f51-b340-4dc857f6cdd2 
```

您将注意到带有会话 cookie 名称`SESSION`及其值`9c3796b4-90d1-4f51-b340-4dc857f6cdd2`的响应。干杯！！！您的插件已成功配置。

如果你想覆盖插件的默认配置，这很容易。您需要更改与该功能相关的属性。例如，默认的 cookie 名称是`SESSION`。要更改 cookie 名称，您需要更改您的`Config.groovy`中的以下属性。

```
springsession.strategy.cookie.name="SPRING_SESSION" 
```

让我们看看一些属性及其对插件的影响。

###### 共同属性

```
springsession.maxInactiveIntervalInSeconds=1800 // Session timeout. default is 1800 seconds
springsession.sessionStore=SessionStore.REDIS // Select session store. Default is REDIS
springsession.defaultSerializer=Serializer.JDK // Serialization mechanism, currently supports JDK & JSON Based. Default is JDK
springsession.strategy.defaultStrategy=SessionStrategy.COOKIE // session tracking mechanism. Default is COOKIE also suports HEADER
springsession.strategy.cookie.name="SESSION" // session cookie name. Default is SESSION
springsession.strategy.httpHeader.headerName="x-auth-token" // session header token name. Default is x-auth-token
springsession.allow.persist.mutable=false // check https://github.com/jeetmp3/spring-session/issues/5 for more detail. Default value is false. 
```

###### Redis 存储具体属性

```
springsession.redis.connectionFactory.hostName="localhost" // Redis server hostname. default is localhost
springsession.redis.connectionFactory.port=6397 // Redis server port. default is 6397
springsession.redis.connectionFactory.timeout=2000 // Redis server connection timeout. default is 2000
springsession.redis.connectionFactory.usePool=true // To use connection pool. default is true
springsession.redis.connectionFactory.dbIndex=0 // Database number. Default is 0
springsession.redis.connectionFactory.password=null // Redis server password. Default is null
springsession.redis.connectionFactory.convertPipelineAndTxResults=true // Specifies if pipelined results should be converted to the expected data type. Default is true

springsession.redis.poolConfig.maxTotal=8 // Pool config maximum active connections. Default is 8
springsession.redis.poolConfig.maxIdle=8 // Pool config maximum idle connections. Default is 8
springsession.redis.poolConfig.minIdle=0 // Pool config minimum active connections. Default is 0

springsession.redis.sentinel.master=null // Redis sentinal master node name. Default is null
springsession.redis.sentinel.nodes=[] // List of Map sentinal nodes. e.g. [[host: 'localhost', port: 6379], [host: '', port: 0] ]. Default is blank
springsession.redis.sentinel.password=null // Redis sentinal password. Default is null
springsession.redis.sentinel.timeout=2000 // Redis sentinal timeout. Default is 2000 
```

###### JSON 序列化的特定属性

```
springsession.redis.jackson.modules = [] // List of Application Jackson modules. This plugin uses the Jackson library to JSON serialization. 
```

##### Json 序列化

默认情况下，这个插件使用 Java 序列化。这种类型的序列化有一个很大的限制，**你需要标记每个 class @Serializable** 。现在想想一些库类，也就是说，库附带的任何类，你没有它的源代码。当您尝试序列化该类时会发生什么？

**`java.io.NotSerializableException`** 是的你说得对！！！！

在现实世界的应用程序中，你想尽可能多地使用库，因为你不想写已经写好的类/例程，为什么不重用它们呢。

JSON 序列化来了。在 JSON 序列化中，您不需要将@Serializable 放在类上。要在插件中使用 JSON 序列化，你需要改变属性

```
springsession.defaultSerializer = Serializer.JSON 
```

就是这样！！！

JSON 序列化将完美地工作，直到您的类有一个默认的构造函数。但是如果任何一个类没有默认的构造函数，那么你就需要为这个类和一个 Jackson 模块类添加 mixin 类或者序列化器/反序列化器。这篇博客将指导你用 Java 参数化构造函数反序列化 json。

我们举个例子。我有下面提到的用户类

```
package demo;

import java.io.Serializable;

/**
 * @author Jitendra Singh.
 */
public class User {

    private String name;

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
} 
```

如您所见，有一个参数化的构造函数。我想让这门课开课。所以我现在要做的是。

###### 1。为用户创建混音

```
package demo;

import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonProperty;

/**
 * @author Jitendra Singh.
 */
public class UserMixin {
    private String name;

    @JsonCreator
    public UserMixin(@JsonProperty("name") String name) {

    }
} 
```

###### 2。创建模块类来注册 mixin

```
package demo;

import com.fasterxml.jackson.core.Version;
import com.fasterxml.jackson.databind.Module;

/**
 * @author Jitendra Singh.
 */
public class SimpleModule extends Module {
    @Override
    public String getModuleName() {
        return "Simple";
    }

    @Override
    public Version version() {
        return Version.unknownVersion();
    }

    @Override
    public void setupModule(SetupContext setupContext) {
        setupContext.setMixInAnnotations(User.class, UserMixin.class);
    }
} 
```

###### 3。用 spring-session 插件注册我的模块类

```
springsession.redis.jackson.modules = ['demo.SimpleModule'] 
```

就是这样。你完了。

在下一篇文章中，我将解释 Mongo 数据存储。编码快乐，干杯！！！