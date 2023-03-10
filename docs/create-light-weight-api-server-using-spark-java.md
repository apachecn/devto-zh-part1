# 使用 Spark Java 创建轻量级 api 服务器

> 原文：<https://dev.to/silentsudo/create-light-weight-api-server-using-spark-java>

大家好，

作为一名移动开发者，我经常面临的一个问题是`server not ready`。
替代产品可用于构建 api-server，我们的目标是创建轻量级 api-server，我们的主要目的是创建最少的可用服务器，我们不会在任何地方部署这些服务器。所以我们将重点放在 Spark Java([http://sparkjava.com](http://sparkjava.com))上。

我们将尝试使用 spark java 创建一个类似 twitter 的 api 服务器。

让我们开始吧:

我们将使用 Intellij IDE 来创建我们的 api-server 应用程序(我仍然喜欢完整的`application`而不是称它为`app` ❤️)

*   文件->新项目->Maven
*   从 archtype 创建->maven-archtype-quickstart
*   填写组 id 和工件详细信息，然后继续
*   让其他默认值保持原样
*   项目名称

完成所有这些后，您将看到准备好使用 maven 项目，
使用具有 main 方法的 app 类添加一个运行配置，然后按下 play 按钮 App 应该会打印出`Hello World!`这就是我们创建的 maven 项目。

给它添加一些服务器代码。

要使用 spark java，我们首先需要向 maven(pom.xml)文件添加 spark-java 依赖项。

让我们前进到[http://sparkjava.com/documentation](http://sparkjava.com/documentation)
复制 maven 依赖项并将其粘贴到 pom.xml 中的 dependencies 标记内

下面是写这篇文章时的样子:

```
<dependency>
    <groupId>com.sparkjava</groupId>
    <artifactId>spark-core</artifactId>
    <version>2.6.0</version><!-- check for the updated version from documentation -->
</dependency> 
```

这个类包含了 HelloWorld api 入门的所有必要内容。

将这些行添加到主方法:

```
Spark.get("/sayhello", (request, response) -> {
            return "Hello buddy!!!";
}); 
```

保存并运行应用程序，然后在浏览器上启动`http://localhost:4567/sayhello`，它应该会响应`Hello buddy!!!`

恭喜你刚刚创建了一个非常小的服务器应用程序。

目前就是这样，在下一节中，我们将配置我们的应用程序，使其行为更像一个 api-server，而不是 hello world。

感谢您的阅读。