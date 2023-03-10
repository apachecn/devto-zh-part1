# Java 微框架

> 原文:# t0]https://dev . to/yorodm/microframework-in-Java-na

# 你应该审查的微观框架。

∞天啊！几个星期的空闲后，我继续谈论微服务框架的话题，现在又以 **Java** 为我最喜欢的另一种语言。既然大家都知道强有力的选手如、[【spring】](https://projects.spring.io/spring-boot/)、我就来谈谈所谓

## [鼠笼](http://www.ratpack.io)

首先，我们有 [Ratpack](http://www.ratpack.io) ，它基本上为我们提供了基于**net**和 *DSL* 的网络服务器来处理路由(非常至[机架](http://rack.github.io) + [Sinatra】作为 plus，我们可以使用](http://sinatrarb.com/)[【lazzybons】](https://github.com/pledbrook/lazybones)生成应用框架。

除了框架中的*核心*之外，还有以下组件:

*   以指南为依赖注入容器。
*   梅特里卡斯·德·乌索。
*   **thmleaf**和**作为模板引擎。**
*   [领事](http://www.consul.io)
*   **Spring Boot**
*   使用 [Spock](http://spockframework.org/) 进行单元测试

## [开机](http://bootique.io)

**boot**邀请我们使用 **Guice** 创建模块化应用程序。与 **Ratpack** 不同，它的编程风格更多的是使用注释，但也使用流畅的界面和闭式。使用**指南**也给我们创建应用程序或扩展框架的方式带来了特殊的味道。最后，这是**【bootie】**的强势特征清单

*   支持使用 [Flyway](http://flyway.orge) 或 [LiquidBase](http://www.liquibase.org/) 进行迁移。
*   通过 **juice** 与其他框架集成
*   梅特里卡斯。
*   **大摇大摆**
*   Rabbitmq
*   利用科特林开发的可能性

## [Rapidoid](http://www.rapidoid.org/)

顾名思义，**rapido**注重速度。我们不仅可以在很短的时间内创建微服务，而且可以创建完整的应用程序。在这里介绍的框架中，只有一个包含了“T2”ui“”的组件以及自己的模板引擎“T4”mustache“”。令我印象深刻的是:

*   实体 a 的自动管理界面**【django】**或**【jhip ter】**。
*   内置模板引擎。
*   支持 **juice**
*   速度+功能

因为我最喜欢的是，我会举一个例子，说明如何创建一个简单的 **Web API** ，使我们能够添加、查询和删除服务请求(我们一辈子都称之为票证)

### 步骤 1。配置 Maven。

作为 **Maven** 和 **Eclipse** 的用户，我将创建一个新项目并添加以下依赖项

```
<dependency>
    <groupId>org.rapidoid</groupId>
    <artifactId>rapidoid-quick</artifactId>
    <version>5.4.6</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 2。创建我的实体。

**fast oid quick**包括 **Hibernate** 、 **JPA** 预配置，所以我们不需要太多的创建实体

```
package com.github.yorodm.rapidoid_example.models;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.validation.constraints.NotNull;

@Entity
public class Ticket {

    @Id
    @GeneratedValue
    public long id;

    @NotNull
    public String title;

    @NotNull
    public String description;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 3。创建我的应用程序和路径

```
public class Main {

    public static void main(String[] args) {
        App.bootstrap(args).auth()
        .jpa()
        .adminCenter();

        On.get("/tickets").json(()
            -> JPA.of(Ticket.class).all());

        On.post("/tickets")
            .json((@Valid Ticket t)
                -> JPA.save(t));
        On.put("/tickets/{id}")
            .json((Long id, @Valid Ticket t)
                -> JPA.update(t));

        On.delete("/tickets/{id}")
            .json((Long id) -> {
                JPA.delete(Ticket.class, id);
                return true;
        });
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 帕索 4。配置。

对于设置，我们在`src/resources/config.yml`中使用**【YAML】**。

```
users:
  root:
    roles:
      - administrator
      - owner
    password: root 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 5。建立要建置的 jar。

我们将把 **Maven** 的`shade`插件设置为创建*Uber*并使我们的生活更加轻松。

```
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-shade-plugin</artifactId>
      <version>3.1.0</version>
      <configuration>
        <transformers>
          <transformer
              implementation=
              "org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
            <mainClass>
                com.github.yorodm.rapidoid_example.Main
            </mainClass>
          </transformer>
        </transformers>
      </configuration>
      <executions>
        <execution>
          <phase>package</phase>
          <goals>
            <goal>shade</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build> 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 6。正在运行应用程序

打包并运行:

```
$ mvn package
$ cd target
$ java -jar rapidoid-example-0.0.1-SNAPSHOT.jar profiles=dev,default 
```

Enter fullscreen mode Exit fullscreen mode

经过几个日志后，我们在我们的主机上有可用的服务，同时还有[管理控制板](http://localhost:8080/_)。