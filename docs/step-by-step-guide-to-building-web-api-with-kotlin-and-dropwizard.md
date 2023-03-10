# 使用 Kotlin 和 Dropwizard 构建 Web API 的分步指南

> 原文：<https://dev.to/tagmg/step-by-step-guide-to-building-web-api-with-kotlin-and-dropwizard>

在这篇文章中，我们将为用 Kotlin 编写的 Web API 服务器奠定基础，并在以后的文章中以此为基础，涵盖数据库访问、认证和部署等主题。

# 为什么是科特林？

Kotlin 是 JVM 的一种优秀语言，当它被宣布为 Android 开发的官方支持语言之一时，引起了很大的反响。

然而，Kotlin 的主要优势在于它惊人的 Java 互操作性，这也使它成为构建后端服务的绝佳选择。

我们当然可以为 Kotlin 使用一个最新的酷框架，比如 [KTOR](http://ktor.io/) ，但是当我们可以利用这个互操作性故事来使用一个令人惊叹的现有 Java 生态系统时，为什么要这样做呢？天知道这个生态系统已经完善了多久了。

# Dropwizard

试图在 JVM 上整合一些更好的 web 开发实践的项目之一是 [Dropwizard](http://www.dropwizard.io/) 。

它包括:

*   HTTP 的 Jetty
*   休息用球衣
*   杰克逊换 JSON
*   Logback 和 slf4j 用于日志记录
*   用于数据库访问的 JDBI 和 Hibernate
*   等等

Dropwizard 本身有非常棒的文档，包括[入门指南](http://www.dropwizard.io/1.1.4/docs/getting-started.html)，但都是 Java 和 Maven，这让你感觉像是从一开始就在编写遗留代码。

过一点点生活，让一切都变成科特林和格雷德怎么样？

# 新 Kotlin 应用

我们将构建一个简单的计算器 web 服务。完整的源代码可以在 [GitHub](https://github.com/groz/kotlin-webapi) 上获得。

最简单的方法是选择一个 Kotlin (JVM)模板，在 IntelliJ 中创建一个 Gradle 项目。

设置`GroupID: to.dev.example`、`ArtifactID: kotlin-webapi`，检查`Use auto-import`，一切就绪。IntelliJ 将为编译 Kotlin 代码设置基本的项目布局。

添加一个`src/main/kotlin/to/dev/example/MainApp.kt`文件，它将包含我们应用程序的入口点:

```
package to.dev.example

fun main(args: Array<String>) {
    println("Hello, Kotlin!")
} 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，IntelliJ 为一个库而不是一个应用程序创建一个 Gradle build。让我们在项目根目录下的`build.gradle`文件中添加几行代码，将它变成一个可运行的应用程序:

```
apply plugin: 'application'
mainClassName = 'to.dev.example.MainAppKt' 
```

Enter fullscreen mode Exit fullscreen mode

注意到`MainAppKt`类名了吗？它从哪里来？

Java 世界中没有类之外的函数，所以 Kotlin 方便地在幕后为我们创建了一个类，命名为`<FileName>Kt`并将`main`函数放在里面。

现在我们可以转到项目根目录，键入:`$ ./gradlew run`让 Gradle 构建并运行我们的适度应用程序。

```
:compileKotlin UP-TO-DATE
:compileJava NO-SOURCE
:copyMainKotlinClasses UP-TO-DATE
:processResources NO-SOURCE
:classes UP-TO-DATE
:run
Hello, Kotlin! 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个正在运行的应用程序，让我们给它添加一些 Dropwizard。

# drowizard 应用程序

Dropwizard 的结构是一组较小的库，因此您可以只添加您需要的库。目前来说,`dropwizard-core`就足够了。

将以下几行添加到同一个`build.gradle`文件:

```
ext.dropwizard_version = '1.1.4'

dependencies {
    compile "io.dropwizard:dropwizard-core:$dropwizard_version"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 配置

Dropwizard 内置了一个很深的配置文件概念，事实上，要运行 Dropwizard 应用程序，首先要做的就是定义从`io.dropwizard.Configuration`派生的配置类。

此配置主要用于区分开发和生产设置，如数据库连接字符串。

Kotlin 使得创建这样简单的类变得非常容易。仅仅一行代码:

```
class CalculatorConfig(val name: String = "unknown") : Configuration() 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们需要一个实际的配置文件来解析成这个配置。
添加一个`config/local.yaml`类到我们项目的根:

```
name: Kotlin Calculator 
```

Enter fullscreen mode Exit fullscreen mode

## 申请

要创建 web 服务，我们需要向 Jersey 注册组件。
组件可以很容易地用标准的、不言自明的`javax.ws.rs`注释来增强:

```
@Path("/")
class CalculatorComponent {
    @Path("/add")
    @GET
    fun add(@QueryParam("a") a: Double, @QueryParam("b") b: Double): Double {
        return a + b
    }

    @Path("/multiply")
    @GET
    fun multiply(@QueryParam("a") a: Double, @QueryParam("b") b: Double): Double {
        return a * b
    }

    @Path("/divide")
    @GET
    fun divide(@QueryParam("a") a: Double, @QueryParam("b") b: Double): Double {
        if (b == .0) {
            throw WebApplicationException("Can't divide by zero")
        }
        return a / b
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

结合上面声明的配置，从`io.dropwizard.Application`派生的一个类构成了 Dropwizard 应用程序的核心，这就是我们将向 Jersey:
注册组件的地方

```
class CalculatorApp : Application<CalculatorConfig>() {
    override fun run(configuration: CalculatorConfig, environment: Environment) {
        println("Running ${configuration.name}!")
        val calculatorComponent = CalculatorComponent()
        environment.jersey().register(calculatorComponent)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:这注册了一个将在请求间共享的单例组件。还有其他更强大的方法来控制为资源提供服务的组件的创建，但这是另一天的主题。

现在创建这个类的一个对象，并从我们的`main`函数:
传递命令行参数给它

```
fun main(args: Array<String>) {
    CalculatorApp().run(*args) // use collection as a varargs
} 
```

Enter fullscreen mode Exit fullscreen mode

# 运行

```
$ ./gradlew run
...
:run
usage: java -jar project.jar [-h] [-v] {server,check} ...

positional arguments:
  {server,check}         available commands

optional arguments:
  -h, --help             show this help message and exit
  -v, --version          show the application version and exit 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们需要向应用程序传递一些命令行参数。
我们应该构建一个工件，并作为一个独立的 Java 应用程序运行它，但是我更喜欢现在把它破解，在`build.gradle`文件:
中为运行任务提供那些参数

```
run {
    args = ['server', 'config/local.yaml']
} 
```

Enter fullscreen mode Exit fullscreen mode

再次运行:

```
$ ./gradlew run
...
Running Kotlin Calculator!
...
INFO  [2017-09-16 21:17:27,181] io.dropwizard.server.ServerFactory: Starting CalculatorApp
...
INFO  [2017-09-16 21:17:27,664] io.dropwizard.jersey.DropwizardResourceConfig: The following paths were found for the configured resources:

    GET     / (to.dev.example.CalculatorComponent)
    GET     /add (to.dev.example.CalculatorComponent)
    GET     /multiply (to.dev.example.CalculatorComponent)

...

INFO  [2017-09-16 21:17:27,681] org.eclipse.jetty.server.AbstractConnector: Started application@720bf653{HTTP/1.1,[http/1.1]}{0.0.0.0:8080}
INFO  [2017-09-16 21:17:27,682] org.eclipse.jetty.server.AbstractConnector: Started admin@360bc645{HTTP/1.1,[http/1.1]}{0.0.0.0:8081}
... 
```

Enter fullscreen mode Exit fullscreen mode

正如你在上面的日志中看到的，web 应用程序开始监听默认端口`8080`和管理端口`8081` :

```
$ curl "localhost:8080/add?a=5&b=10"
15
$ curl "localhost:8080/multiply?a=5&b=10"
50
$ curl "localhost:8080/divide?a=5&b=0"
{"code":500,"message":"Can't divide by zero"} 
```

Enter fullscreen mode Exit fullscreen mode

瞧，我们用不到 50 行的 Kotlin 代码编写了一个自包含的 web 服务，每秒能够处理 30，000-50，000 个请求。

在接下来的文章中，我们将讨论测试、服务 JSON 对象、创建用于部署、数据库访问和认证的独立应用程序。