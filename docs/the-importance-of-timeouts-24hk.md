# 超时的重要性

> 原文：<https://dev.to/brightdevs/the-importance-of-timeouts-24hk>

暂停并不是一件令人兴奋的事情。它们不会立即增加可察觉的价值。他们很难~~猜对~~并强迫一个人[考虑难以解决的问题](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance#Byzantine_Generals.27_Problem)。事实上，根据我的经验，只有当我们的软件停止工作或者将要停止工作时，才会考虑超时。这是一个巨大的遗憾，因为在我看来，谨慎应用超时可以极大地提高软件的弹性。

[![Man with a wrist clock](img/dd932d8f7d3d0a115dee2d9a1e3e2f15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pLt5y-9j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/th75wdlhclkxbjb9ujo5.jpeg)

## 一个应用实例

让我们考虑一个使用 [Spring Initializr](https://start.spring.io/) 生成的 Spring Boot 应用程序的简单例子。应用程序将只公开默认定义健康检查端点的[致动器](https://spring.io/guides/gs/actuator-service/) API。我们的示例还将配置一个邮件模块。

`build.gradle`的依赖部分:

```
dependencies {
    compile('org.springframework.boot:spring-boot-starter-actuator')
    compile('org.springframework.boot:spring-boot-starter-mail')
    compile('org.springframework.boot:spring-boot-starter-web')
    compile("org.jetbrains.kotlin:kotlin-stdlib-jre8:${kotlinVersion}")
    compile("org.jetbrains.kotlin:kotlin-reflect:${kotlinVersion}")
} 
```

Enter fullscreen mode Exit fullscreen mode

典型的健康检查端点验证应用程序集成点是否正常工作。如果服务与数据库对话，则连接建立并验证。检查可用磁盘空间。如果服务通过 SMTP 发送电子邮件，则与服务器建立连接。

当您包括 Spring Boot 执行器时，健康检查会自动发现并启用。默认情况下，`/health`路径用于端点。显然需要配置 SMTP 服务器主机、端口和凭证。至少需要主机条目，例如`spring.mail.host=localhost`。*出于调试目的，可使用`management.security.enabled=false`禁用致动器安全，以验证致动器执行了哪些健康检查。*

我们例子的最后一部分是简单的应用程序代码:

```
@SpringBootApplication
class Application {
    companion object {
        @JvmStatic
        fun main(args: Array<String>) {
            SpringApplication.run(Application::class.java, *args)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当您请求`/health`时，API 将返回类似于
的响应

```
HTTP/1.1 200 
Content-Type: application/vnd.spring-boot.actuator.v1+json;charset=UTF-8
Date: Mon, 23 Oct 2017 08:08:32 GMT
Transfer-Encoding: chunked
X-Application-Context: application

{
    "diskSpace": {
        "free": 105755779072,
        "status": "UP",
        "threshold": 10485760,
        "total": 499046809600
    },
    "mail": {
        "location": "localhost:-1",
        "status": "UP"
    },
    "status": "UP"
} 
```

Enter fullscreen mode Exit fullscreen mode

一个应用健康 API，就像我们例子中的 API，经常被挂接到外部监控软件中。监视器定期向目标应用程序询问其健康状况，例如每 5 秒一次。

## 搬起石头砸自己的脚

上面的例子有一个可以杀死生产服务器的问题。更重要的是，通常被监控的其他指标(例如 CPU 和内存使用)不会警告即将到来的可怕的服务停顿。该应用程序也不会受到发送大量请求或电子邮件的影响。

假设每 5 秒检查一次健康端点，并且 SMPT 服务器存在**间歇性**问题。健康端点将**正确地**尝试连接到 SMTP 服务器，并不时地以错误响应。根据我的经验，当引入健康检查时，通常需要一段时间来调整监视器阈值，以便我们消除错误警报。因此，当我们认为间歇性错误是由过于敏感的阈值引起时，就很容易忽略它们。然而，这些被忽略的错误会导致我们的服务器停止响应**的任何请求**。

[![Man with a wrist clock](img/f185b52b59c83ad9ff01aa51b25d52f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jph886-4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vug034bebmi4j31wk4im.jpg)

为什么会这样你问我答。**没有配置超时！**

邮件服务器健康检查使用 [`javax.mail.Service.connect`](https://docs.oracle.com/javaee/6/api/javax/mail/Service.html#connect(java.lang.String,%20java.lang.String)) 引擎盖下。由于各种原因，尝试建立 TCP 连接可能会比平时花费更多的时间。不幸的是**`javax.mail.*`[使用的默认](https://javaee.github.io/javamail/docs/api/com/sun/mail/smtp/package-summary.html)**超时是**无限** 。等待连接建立的线程不能为其他请求服务，即使它几乎不使用任何 CPU。嵌入式 Tomcat 在 Spring Boot 应用程序中使用的默认最大线程池大小是 200。假设被阻止的连接尝试每小时发生两次，我们的应用程序将在 4 天后停止工作。

## 永远不要使用无限超时

如您所见，很容易忽略配置超时的需求。公平地说， [Spring Boot 文件](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-email.html)明确指出:

> 特别是，某些默认超时值是无限的，您可能希望更改该值以避免线程被无响应的邮件服务器阻塞:
> 
> ```
> spring.mail.properties.mail.smtp.connectiontimeout=5000
> spring.mail.properties.mail.smtp.timeout=3000
> spring.mail.properties.mail.smtp.writetimeout=5000 
> ```

在我的选择中，任何库或框架要么应该强迫程序员配置超时，要么应该有一些**合理的默认值**。不幸的是，这并不总是能够在不中断更改的情况下引入它们，因此我们应该**检查**调用任何**外部服务**时使用的超时。

## 到处都需要超时

想象一个控制器动作方法，它将一行插入数据库。让我们进一步假设端点每秒被调用 50 次，通常需要 100ms 才能完成。事情进展顺利，直到我们遇到数据库的间歇性故障，现在插入需要 2 秒钟才能完成。调用 API 的客户端不会变慢。更多的请求线程被阻塞，更多的数据库连接被从池中取出。一旦所有数据库连接都在使用中，并且所有其他 API 端点都开始失败。这是级联故障的一个例子，即一个组件中的问题传播到其他组件。当在控制器动作和与数据库的交互上配置了超时时，更容易避免这样的问题。

如果我们希望我们的服务具有弹性，每个 API 端点**都应该配置一个超时**。不幸的是，这条规则很容易被忽视。此外，一些框架甚至没有公开这种能力。即使在非常流行的 Spring Mvc 中，除了使用[异步方法](https://spring.io/guides/gs/async-method/)之外，我也找不到设置这种超时的方法。幸运的是，有一些库，例如 [Hystrix](https://github.com/Netflix/Hystrix) 可以解决这个问题，并且很容易集成。

这里简单回顾一下应该配置超时的情况，这是一个简短且不完整的列表:

*   控制器动作
*   数据库查询、语句
*   数据库池交互
*   线程池交互
*   API 客户端，例如 HTTP、SOAP、SMTP

我将在下面的帖子中描述如何处理这些案例。

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)