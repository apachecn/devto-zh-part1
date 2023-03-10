# 用 Swagger 记录的 Scala REST API(第 1 部分，共 2 部分)

> 原文：<https://dev.to/svinci/scala-rest-api-documented-with-swagger-part-1-of-2>

在这个帖子系列中，我们将使用 SBT、Scala 和 Scalatra 构建一个 REST API。我很喜欢 Scalatra 的一点是，它有一个库，可以使用 Swagger 和很少的 efort 来记录你的 API。

我们的 API 将非常简单。它将管理一个专业人员的目录，但只有在客户经过身份验证的情况下，才会提供联系信息。

*缺点:Scalatra swagger 只支持 swagger v1，现已弃用。有工具可以将 swagger v1 JSONs 转换为 swagger v2，甚至转换为 RAML，所以这并不真正困扰我。*

## 我们将在这一部分上构建什么

第 1 部分将只包括项目的设置、状态端点和检索 swagger 文档的 servlet 的实现。

第二部分将讨论管理专业人员的端点、认证过滤器、独立构建(jar 部署)和以交互方式查看 API 文档的 UI。

## 前提条件

我假设你熟悉 Scala 语言、SBT 和 REST。

要完成本教程，你需要安装 JDK 8(开放 JDK 也可以)和 [SBT](http://www.scala-sbt.org/download.html) 。

我提供的所有安装、运行和操作的命令都是在 Linux Mint 18 上测试的。他们可能会在 Mac OS 上工作，没有任何问题。如果你在 windows 上工作，我真的很抱歉。

## 设置

我将展示如何手工创建这个项目，但是也有项目模板和您可以使用的东西。我认为 SBT 有一个生成空项目的命令，但 lighbend activator 也是一个从模板开始的好工具。

首先，让我们创建我们的项目目录运行`mkdir scala-scalatra-swagger-example`。

现在，这些是您需要的目录:

*   项目
*   资源中心/主要/资源
*   src/main/scala
*   src/main/webapp/WEB-INF

在`project`目录下，需要创建一个名为`build.properties`的文件，内容如下:

```
sbt.version=0.13.13 
```

Enter fullscreen mode Exit fullscreen mode

我们定义我们的项目将使用 sbt 0.13.13 构建。

同样，在`project`目录下，您需要一个名为`plugins.sbt`的文件，其内容如下:

```
addSbtPlugin("org.scalatra.sbt" % "scalatra-sbt" % "0.5.1")

addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.4")

addSbtPlugin("org.scalariform" % "sbt-scalariform" % "1.6.0") 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们正在导入 scalatra SBT 插件，需要与 scalatra 一起工作(它将提供一些工具与 Jetty 和东西一起工作)。然后，我们导入 assembly，我们将使用它来构建独立的 jar。最后，我们添加了 scalariform，所以我们的代码在编译时被格式化。

现在，在`src/main/webapp/WEB-INF`下，我们需要一个名为`web.xml`的文件，其内容如下:

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app 
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
      version="3.0">

  <!--
    This listener loads a class in the default package called ScalatraBootstrap.
    That class should implement org.scalatra.LifeCycle.  Your app can be
    configured in Scala code there.
  -->
  <listener>
    <listener-class>org.scalatra.servlet.ScalatraListener</listener-class>
  </listener>
</web-app> 
```

Enter fullscreen mode Exit fullscreen mode

这基本上告诉 jetty 我们唯一的监听器是 Scalatra 提供的。

现在，在`src/main/resources`下，我们需要我们的应用程序配置文件，名为`application.conf`。

```
application.port=8080 
```

Enter fullscreen mode Exit fullscreen mode

是的，它只包含 API 将监听的端口，但我想展示配置如何在这样的应用程序上工作，因为这是生产中的真实需求。

此外，日志是生产中的必需品，因此，在`src/main/resources`下，我们也需要我们的`logback.xml`。

```
<configuration>

  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>[%d] [%level] [tid: %X{tid:-none}] [%t] [%logger{39}] : %m%n%rEx{10}</pattern>
      <charset>utf8</charset>
    </encoder>
  </appender>

  <root level="INFO">
    <appender-ref ref="CONSOLE"/>
  </root>

</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

这只定义了一个控制台 appender(将日志发送到标准输出)，具有真正标准的模式(唯一奇怪的是`tid`这个东西，我们稍后会谈到)。

现在我们用一个`build.sbt`文件把所有东西粘在一起。

```
import com.typesafe.sbt.SbtScalariform
import com.typesafe.sbt.SbtScalariform.ScalariformKeys
import org.scalatra.sbt._

import scalariform.formatter.preferences._

val ScalatraVersion = "2.5.0"

ScalatraPlugin.scalatraSettings

organization := "com.svinci.professionals"

name := "api"

version := "0.0.1-SNAPSHOT"

scalaVersion := "2.11.8"

resolvers += Classpaths.typesafeReleases

SbtScalariform.scalariformSettings

ScalariformKeys.preferences := ScalariformKeys.preferences.value
  .setPreference(AlignSingleLineCaseStatements, true)
  .setPreference(DoubleIndentClassDeclaration, true)
  .setPreference(AlignParameters, true)
  .setPreference(AlignArguments, true)

libraryDependencies ++= Seq(
  "org.scalatra"        %%  "scalatra"          % ScalatraVersion,
  "org.scalatra"        %%  "scalatra-json"     % ScalatraVersion,
  "org.scalatra"        %%  "scalatra-swagger"  % ScalatraVersion,
  "org.json4s"          %%  "json4s-native"     % "3.5.0",
  "org.json4s"          %%  "json4s-jackson"    % "3.5.0",
  "com.typesafe"        %   "config"            % "1.3.1",
  "ch.qos.logback"      %   "logback-classic"   % "1.1.5"             % "runtime",
  "org.eclipse.jetty"   %   "jetty-webapp"      % "9.2.15.v20160210"  % "container;compile",
  "javax.servlet"       %   "javax.servlet-api" % "3.1.0"             % "provided"
)

assemblyJarName in assembly := "professionals-api.jar"

enablePlugins(JettyPlugin) 
```

Enter fullscreen mode Exit fullscreen mode

这个`build.sbt`在干什么？

*   在文件的顶部，我们正在导入我们的插件和我们需要建立项目的东西。
*   然后我们定义一个包含 scalatra 版本(2.5.0)并应用`ScalatraPlugin.scalatraSettings`的值。
*   之后，我们定义一些工件信息(组织、名称、版本、scala 版本)，将 typesafe 存储库(lightbend 过去被称为 typesafe)添加到我们的解析器中，然后我们配置 scalariform。
*   然后，我们的依赖关系被定义。注意 scalatra 依赖、json4s、config、logback 和 jetty。
*   然后，我们正在配置 assembly，所以所有东西都打包在一个名为`professionals-api.jar`的 jar 下
*   最后，我们启用了 jetty 插件(它附带了 scalatra 插件)。

此时，我们可以在项目的根目录上运行`sbt`来查看它的加载是否正确，甚至可以编译它(虽然没有要编译的代码)。

现在是时候用 IDE 打开这个项目了(是的，到目前为止我一直使用 vim)。

## 效用对象和性状

我们现在将为我们的代码创建一些基础设施。首先，让我们创建我们的包`com.svinci.professionals.api.infrastructure`。在这个包下，我们来写以下内容(代码用注释解释):

*   一个名为`Configuration.scala`的文件，包含以下内容:

```
package com.svinci.professionals.api.infrastructure

import com.typesafe.config.ConfigFactory

/**
  * This will provide a singleton instance of our configuration.
  * Also, it will encapsulate typesafe.config, so the rest of the application doesn't need to know about the configuration library we are using.
  */
object Configuration {

  /**
    * This is our configuration instance. Private and immutable.
    */
  private[this] val configuration = ConfigFactory.load()

  /**
    * Methods like this one should be defined to access any type of configuration from its key.
    * The reason we do it is to define an interface that makes sense for our application, and make the rest of the code
    * agnostic to what library we are using. Just a good practice.
    * @param key Configuration key.
    * @return The configured Int.
    */
  def getInt(key: String): Int = configuration.getInt(key)

} 
```

Enter fullscreen mode Exit fullscreen mode

*   一个名为`ApiInformation.scala`的文件，包含以下内容:

```
package com.svinci.professionals.api.infrastructure

import org.scalatra.swagger.{ApiInfo, Swagger}

/**
  * Information of our API as a whole.
  */
object ProfessionalsApiInfo extends ApiInfo(
  title = "professionals-api",
  description = "Professionals CRUD operations.",
  termsOfServiceUrl = "some terms of service URL",
  contact = "some contact information",
  license = "MIT",
  licenseUrl = "http://opensource.org/licenses/MIT"
)

/**
  * Swagger instance for our API. It's defined  as an object so we have only one instance for all our resources.
  */
object ProfessionalsApiSwagger extends Swagger(swaggerVersion = Swagger.SpecVersion, apiVersion = "1.0.0", apiInfo = ProfessionalsApiInfo) 
```

Enter fullscreen mode Exit fullscreen mode

*   一个名为`ServletSupport.scala`的文件，包含以下内容:

```
package com.svinci.professionals.api.infrastructure

import java.util.{Date, UUID}

import org.json4s.{DefaultFormats, Formats}
import org.scalatra._
import org.scalatra.json._
import org.scalatra.swagger.SwaggerSupport
import org.slf4j.{LoggerFactory, MDC}

/**
  * We'll have a couple servlets probably (a status endpoint, the CRUD servlet for our professionals, and if we deploy this to production we'll probably add some more),
  * so it's convenient to have the things every servlet will need to define in one trait to extend it.
  * 
  * This trait extends ScalatraServlet and adds json and swagger support.
  */
trait ServletSupport extends ScalatraServlet with JacksonJsonSupport with SwaggerSupport {

  /**
    * As we are going to document every endpoint of our API, we'll need our swagger instance in everyone of our servlets.
    */
  override protected implicit def swagger = ProfessionalsApiSwagger

  /**
    * This is a logger... to log stuff.
    */
  private[this] val logger = LoggerFactory.getLogger(getClass)
  /**
    * Scalatra requires us to define an implicit Formats instance for it to know how we want JSONs to be serialized/deserialized.
    * It provides a DefaultFormats that fill all our needs today, so we'll use it. 
    */
  protected implicit lazy val jsonFormats: Formats = DefaultFormats

  /**
    * Before every request made to a servlet that extends this trait, the function passed to `before()` will be executed.
    * We are using this to :
    *   - Set the Content-Type header for every request, as we are always going to return JSON.
    *   - Set the date to the request, so we can calculate spent time afterwards.
    *   - Generate a transaction identifier, an add it to the MDC, so we know which lines of logs were triggered by which request.
    *   - Log that a request arrived.
    */
  before() {

    contentType = "application/json"
    request.setAttribute("startTime", new Date().getTime)
    MDC.put("tid", UUID.randomUUID().toString.substring(0, 8))

    logger.info(s"Received request ${request.getMethod} at ${request.getRequestURI}")

  }

  /**
    * NotFound handler. We just want to set the status code, and avoid the huge stack traces scalatra returns in the body.
    */
  notFound {

    response.setStatus(404)

  }

  /**
    * After every request made to a servlet that extends this trait, the function passed to `after()` will be executed.
    * We are using this to:
    *   - Retrieve the start time added in the `before()` handler an calculate how much time the API took to respond.
    *   - Log that the request handling finished, with how much time it took.
    */
  after() {

    val startTime: Long = request.getAttribute("startTime").asInstanceOf[Long]
    val spentTime: Long = new Date().getTime - startTime
    logger.info(s"Request ${request.getMethod} at ${request.getRequestURI} took ${spentTime}ms")

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 状态端点

我们现在将编写一个状态端点，它将总是返回一个 JSON，内容如下:

```
{
  "healthy": true
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您有一个数据库，或者您依赖的 API，您可以在那里添加它们的状态。首先，让我们创建我们的包`com.svinci.professionals.api.domain.status`，在这个包下，编写以下代码(代码用注释解释):

*   一个名为`Status.scala`的文件，包含以下内容:

```
package com.svinci.professionals.api.domain.status

/**
  * This is the object our status endpoint will convert to JSON and return.
  */
case class Status(healthy: Boolean) 
```

Enter fullscreen mode Exit fullscreen mode

*   一个名为`StatusService.scala`的文件，包含以下内容:

```
package com.svinci.professionals.api.domain.status

/**
  * We are using cake pattern to solve dependency injection without using any library. You can find a really good explanation of this pattern at http://www.cakesolutions.net/teamblogs/2011/12/19/cake-pattern-in-depth.
  * 
  * This is the component that defines a StatusService interface (trait, actually), and names an instance of it.
  */
trait StatusServiceComponent {

  /**
    * This is the definition of the instance of StatusService an implementation of this component will hold.
    */
  def statusServiceInstance: StatusService

  /**
    * StatusService interface definition.
    */
  trait StatusService {

    /**
      * Retrieve the application status.
      * @return The application status.
      */
    def status: Status

  }

}

/**
  * Default StatusServiceComponent implementation.
  */
trait DefaultStatusServiceComponent extends StatusServiceComponent {

  /**
    * Here, we define how the DefaultStatusService is created.
    */
  override def statusServiceInstance: StatusService = new DefaultStatusService

  /**
    * Default StatusService implementation.
    */
  class DefaultStatusService extends StatusService {

    /**
      * @inheritdoc
      */
    override def status: Status = Status(healthy = true)

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

*   一个名为`StatusServlet.scala`的文件，包含以下内容:

```
package com.svinci.professionals.api.domain.status

import com.svinci.professionals.api.infrastructure.ServletSupport

/**
  * We are using cake pattern to solve dependency injection without using any library. You can find a really good explanation of this pattern at http://www.cakesolutions.net/teamblogs/2011/12/19/cake-pattern-in-depth.
  *
  * As this is an entry point to our application, there is no need to create an interface (it's a servlet after all, so there are no functions exposed).
  */
trait StatusServletComponent {

  /**
    * As defined by cake pattern, with self type annotations we are defining that any class that extends this trait, needs to extend StatusServiceComponent too.
    * This makes the interface and instance defined by StatusServiceComponent available in this trait.
    */
  this: StatusServiceComponent =>

  /**
    * This is the StatusServlet instance held by this component. Notice that we are instantiating StatusServlet passing the statusServiceInstance provided by StatusServiceComponent.
    */
  def statusServletInstance: StatusServlet = new StatusServlet(statusService = statusServiceInstance)

  /**
    * This is the scalatra servlet that will serve our status endpoint.
    */
  class StatusServlet(val statusService: StatusService) extends ServletSupport {

    /**
      * This value defines the documentation for this endpoint. We are giving the endpoint a name, the return type and a description/summary.
      */
    private[this] val getStatus = apiOperation[Status]("status") summary "Retrieve API status."

    /**
      * We are routing our status endpoint to the root of this servlet, and passing to scalatra our apiOperation.
      */
    get("/", operation(getStatus)) {
      statusService.status
    }

    /**
      * This is the description of this servlet, requested by swagger.
      */
    override protected def applicationDescription: String = "API Status."

  }

}

/**
  * This is the default instance of StatusServletComponent. Here we define that the StatusServletComponent will use the DefaultStatusServiceComponent.
  */
object DefaultStatusServletComponent extends StatusServletComponent with DefaultStatusServiceComponent 
```

Enter fullscreen mode Exit fullscreen mode

现在我们回到名为`com.svinci.professionals.api.infrastructure`的包，创建一个名为`Module.scala`的文件，内容如下:

```
package com.svinci.professionals.api.infrastructure

import com.svinci.professionals.api.domain.status.DefaultStatusServletComponent

/**
  * We are using cake pattern to solve dependency injection without using any library. You can find a really good explanation of this pattern at http://www.cakesolutions.net/teamblogs/2011/12/19/cake-pattern-in-depth.
  *
  * In this object we'll hold all the instances required by our application.
  */
object Module {

  /**
    * Default instance of StatusServlet.
    */
  def statusServlet: DefaultStatusServletComponent.StatusServlet = DefaultStatusServletComponent.statusServletInstance

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了运行我们的 API，我们需要一个应用程序来运行，就像任何 scala 应用程序一样。在名为`com.svinci.professionals.api`的包下，我们将编写一个名为`JettyLauncher.scala`的文件，内容如下:

```
package com.svinci.professionals.api

import com.svinci.professionals.api.infrastructure.Configuration
import org.eclipse.jetty.server.Server
import org.eclipse.jetty.servlet.DefaultServlet
import org.eclipse.jetty.webapp.WebAppContext
import org.scalatra.servlet.ScalatraListener

/**
  * This is the MainClass of our application.
  */
object JettyLauncher extends App {

  /**
    * Server instantiation. We are retrieving the port we need to listen at from our Configuration object.
    */
  val server = new Server(Configuration.getInt("application.port"))

  /**
    * Web application context instantiation.
    * This class will hold the context path, the resource base, the event listener and one servlet.
    */
  val context = new WebAppContext()

  context setContextPath "/"
  context.setResourceBase("src/main/webapp")
  context.addEventListener(new ScalatraListener)  // We use scalatra listener as event listener.
  context.addServlet(classOf[DefaultServlet], "/")  // We don't need to add our servlets here, we're going to add them using the scalatra life cycle.

  server.setHandler(context)  // We add the WebAppContext to the server.

  server.start()  // Start the server.
  server.join()  // Join the server's thread pool so the application doesn't quit.

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要将我们的 servlets 添加到 scalatra lyfe 循环中，所以我们在`src/main/scala`目录下(在任何包之外)编写一个名为`ScalatraBootstrap.scala`的文件，其内容如下:

```
package com.svinci.professionals.api

import javax.servlet.ServletContext

import com.svinci.professionals.api.infrastructure.Module
import org.scalatra._
import org.slf4j.LoggerFactory

/**
  * This class in looked up by scalatra and automatically instantiated. Here we need to code all the start up code of our API.
  */
class ScalatraBootstrap extends LifeCycle {

  private[this] val logger = LoggerFactory.getLogger(getClass)

  /**
    * In the method we need to mount our servlets to the ServletContext provided as parameter.
    * Any additional startup code should be wrote here too (warm up, scheduled tasks initialization, etc.).
    */
  override def init(context: ServletContext) {

    logger.info(context.getContextPath)

    logger.info("Mounting Changas API servlets.")
    context.mount(Module.statusServlet, "/professionals-api/status", "status")
    logger.info(s"API started.")

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们将状态 servlet 安装到路径`/professionals-api/status`，因此我们在 servlet 中进行的所有路由都与该路径相关。我们传递给`mount`方法的第三个参数是我们分配给 servlet 的名称。

这个类需要在那个位置，这样 scalatra 才能找到它。如果你把它放在其他地方，你会看到一个断言错误:`java.lang.AssertionError: assertion failed: No lifecycle class found!`。

现在是时候测试我们的服务器和状态端点了。通过在项目的根目录下运行`sbt`来引导 sbt 控制台，并在内部运行`jetty:start`。执行该命令后，您将拥有对 sbt 控制台的控制权，要停止 API，您可以运行`jetty:stop`。当然，`CTRL + C`也可以，但那也会关闭 sbt 控制台。

现在可以测试 API 了:

```
$ curl http://localhost:8080/professionals-api/status
{"healthy":true} 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在停止服务器，我们继续编码。

## 招摇文档

现在，我们将创建返回端点文档的 servlet。

首先，我们需要创建一个名为`com.svinci.professionals.api.domain.docs`的包，在这个包下，我们将编写一个名为`DocsServlet.scala`的文件，内容如下:

```
package com.svinci.professionals.api.domain.docs

import com.svinci.professionals.api.infrastructure.ProfessionalsApiSwagger
import org.scalatra.ScalatraServlet
import org.scalatra.swagger.NativeSwaggerBase

/**
  * This servlet, as is, will be able to return swagger v1 JSONs. This is the entry point to our documentation.
  */
class DocsServlet extends ScalatraServlet with NativeSwaggerBase {

  /**
    * Application swagger global instance.
    */
  override protected implicit def swagger = ProfessionalsApiSwagger

} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的 servlet，所以我不想在这里做蛋糕模式，这没有意义。现在，在`Module.scala`对象中，我们需要添加一个新函数来检索这个 servlet 的实例:

```
 /**
    * Swagger documentation servlet instance.
    */
  def docsServlet: DocsServlet = new DocsServlet 
```

Enter fullscreen mode Exit fullscreen mode

一旦一切就绪，我们就将新的 servlet 挂载到`ScalatraBootstrap`上的`ServletContext`。

```
context.mount(Module.docsServlet, "/docs", "docs") 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以再次启动我们的服务器并进行测试:

```
 $ curl http://localhost:8080/docs
{"apiVersion":"1.0.0","swaggerVersion":"1.2","apis":[{"path":"/professionals-api/status","description":"API Status."}],"authorizations":{},"info":{}}

$ curl http://localhost:8080/docs/professionals-api/status
{"apiVersion":"1.0.0","swaggerVersion":"1.2","resourcePath":"/professionals-api/status","produces":["application/json"],"consumes":["application/json"],"protocols":["http"],"apis":[{"path":"/professionals-api/status/","operations":[{"method":"GET","summary":"Retrieve API status.","position":0,"notes":"","deprecated":false,"nickname":"status","parameters":[],"type":"Status"}]}],"models":{"Status":{"id":"Status","name":"Status","qualifiedType":"com.svinci.professionals.api.domain.status.Status","required":["healthy"],"properties":{"healthy":{"position":0,"type":"boolean"}}}},"basePath":"http://localhost:8080"} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们的 API 的一般信息在`/docs`可以找到，然后在`/docs/professionals-api/status`你会找到安装在`/professionals-api/status`上的 servlet 的文档。

## 结论

这是后系列的这一部分。我们有一个正在工作的 API，有一个状态端点和 swagger 文档。

现在第二部分还没有写，但几天后我会写好，我会把链接放在这里。

这个例子的代码可以在[这里](https://github.com/svinci/scala-scalatra-swagger-example)找到。

评论里见！