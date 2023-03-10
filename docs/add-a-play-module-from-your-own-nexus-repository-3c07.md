# 从您自己的 Nexus 存储库中添加一个播放模块

> 原文：<https://dev.to/erwyn/add-a-play-module-from-your-own-nexus-repository-3c07>

[![Add a Play module from your own Nexus Repository](img/1acf9a4848d55f3e9947ca278d3376cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HKLn_vOA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erwyn.piwany.com/conteimg/2017/09/typesafe_sbt_svg-4.svg)

那几天我主要在做[剧！Java 的框架](https://www.playframework.com/)。我们处于微服务设置中，因此我们最终在不同的服务之间有一些共同的代码。我们决定制作游戏模块，并导入到不同的项目中。

## 创建模块

为了做到这一点，我们用 SBT 和我们自己的 Nexus 库创建了一个带有几个实用程序类的项目。最终的配置是这样的:

```
name := "some-module"
organization := "my.organization"
version := "1.0.0-SNAPSHOT"
scalaVersion := "2.12.2"
lazy val root = (project in file(".")).enablePlugins(PlayJava)

libraryDependencies ++= Seq(
    guice,
    "com.rabbitmq" % "amqp-client" % "4.2.0",
    "org.mockito" % "mockito-core" % "2.10.0" % "test"
)

val nexusHost = System.getenv("NEXUS_HOST")
val nexusUser = System.getenv("NEXUS_USER")
val nexusPassword = System.getenv("NEXUS_PASSWORD")

publishTo := {
    val httpNexus = "http://" + nexusHost
    if (version.value.trim.endsWith("SNAPSHOT"))
        Some("snapshots" at httpNexus + "/repository/maven-snapshots")
    else
        Some("releases" at httpNexus + "/repository/maven-releases")
}

credentials += Credentials("Sonatype Nexus Repository Manager", nexusHost, nexusUser, nexusPassword) 
```

Enter fullscreen mode Exit fullscreen mode

然后，发布变得超级容易，我们只需要运行`sbt publish`就可以了！该模块发布在我们自己的库中。

## 在你的项目中使用它

现在到了第二部分，在项目中实际使用你的模块。为此，理论上，您需要做的就是添加一个库依赖:

```
libraryDependencies ++= Seq(
    "my.organization" % "some-module" % "1.0.0-SNAPSHOT",
    "com.rabbitmq" % "amqp-client" % "4.2.0"
) 
```

Enter fullscreen mode Exit fullscreen mode

但这还不够。

首先，您需要添加一个解析器来为您的模块指定新的存储库:

```
resolvers += "My Snapshots" at "https://my.url/repository/maven-snapshots/" 
```

Enter fullscreen mode Exit fullscreen mode

您还需要您的凭据才能访问它:

```
credentials += Credentials("Sonatype Nexus Repository Manager", "my.url", nexusUser, nexusPassword) 
```

Enter fullscreen mode Exit fullscreen mode

现在你们都很兴奋，并且输入了`sbt clean update`...它悲惨地失败了。

现在，事情是这样的，SBT 会在`https://my.url/repository/maven-snapshots/my/organization/some-module/1.0.0-SNAPSHOT/...`试图找到你的模块。但是如果你仔细观察你的 Nexus，模块实际上在`/my/organization/some-module_2.12/1.0.0-SNAPSHOT/...`。注意到这个`2.12`了吗？这是你的 Scala 版本，这个其实挺重要的。所以现在，你有三个选择。

*   通过做`crossPaths := false`移除交叉路径(高度**不推荐**)
*   通过依赖`some-module_2.12`而不仅仅是`some-module`来指定 Scala 版本
*   使用`%%`语法让 SBT 添加这个 Scala 版本，这是我们选择的解决方案

这是我们最后的库依赖声明:

```
libraryDependencies ++= Seq(
    "my.organization" %% "some-module" % "1.0.0-SNAPSHOT",
    "com.rabbitmq" % "amqp-client" % "4.2.0"
) 
```

Enter fullscreen mode Exit fullscreen mode

而完整的科学档案:

```
name := """my_service"""
organization := "my.organization"
version := "1.0"
packageName in Universal := "my_service"

lazy val root = (project in file(".")).enablePlugins(PlayJava)

scalaVersion := "2.12.2"

val nexusUser = System.getenv("NEXUS_USER")
val nexusPassword = System.getenv("NEXUS_PASSWORD")

resolvers += "My Snapshots" at "https://my.url/repository/maven-snapshots/"
credentials += Credentials("Sonatype Nexus Repository Manager", "my.url", nexusUser, nexusPassword)

libraryDependencies ++= Seq(
  guice,
  "my.organization" %% "some-module" % "1.0.0-SNAPSHOT",
  "com.rabbitmq" % "amqp-client" % "4.0.2"
) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，将要附加的 Scala 版本是你的`scalaVersion`声明的一个次要版本，所以这里是`2.12`。

你现在可以走了，你可以回到你的玩乐中去了！。