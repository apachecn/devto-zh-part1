# 黄瓜和 Spring Boot

> 原文：<https://dev.to/grahamcox82/cucumber-and-spring-boot>

我是自动化测试的忠实粉丝。无论是工作还是个人，它只是让生活更轻松。我不只是在谈论单元测试。集成/验收/验证/E2E 测试(取决于你喜欢什么术语)可以是一个非常有价值的工具，给你从单元测试中得到的东西一个完全不同的覆盖级别。

然而，为了做到这一点，您需要能够实际运行您想要测试的堆栈。并且您需要能够容易地编写您想要针对堆栈运行的测试。

对于这种级别的测试，我是黄瓜的忠实粉丝——为此有一个 Java Runner(T2)的 T3。这使得测试定义和执行逻辑的实际代码之间完全分离，这意味着可以在不改变业务定义的情况下改变测试的实现。

Spring Boot 和 Cucumber Spring 的集成，使得作为测试引擎的一部分启动服务变得非常容易。Spring 上下文启动一次，使用它执行所有特性，然后 Spring 上下文关闭。它甚至支持在随机端口上启动应用程序监听，这样就不会有冲突。

那么，你是如何做到的呢？黄瓜测试分为三个不同的部分。有测试运行器、特征文件和步骤定义。

Test Runner 是一个简单的 JUnit 类，它简单地使用了 Cucumber JUnit Runner，并完成了这里的所有繁重工作。我的典型样子:

```
/**
 * Test runner for all of the tests
 */
@RunWith(Cucumber::class)
@CucumberOptions(tags = arrayOf("~@ignore"),
        format = arrayOf(
                "pretty",
                "html:target/site/cucumber/cucumber",
                "json:target/failsafe-reports/cucumber.json"
        ),
        strict = true)
internal class CucumberIT 
```

Enter fullscreen mode Exit fullscreen mode

然后，特性文件存在于测试运行程序所在的同一个包中的任何地方——它将在运行时找到它们。

步骤定义是它变得聪明的地方。Cucumber-Spring 允许您指定一个 Spring 上下文，它被启动并用于构建您的步骤定义。这里的要求是，对于整个测试套件，您只能使用一个 Spring 上下文。这意味着我们需要一个包含我们的测试 beans 的 Spring 上下文，同时还要导入 Spring Boot 应用程序来运行。

我们实现这一点的方法是在一个步骤定义上使用两个注释——哪一个并不重要。(在后面的帖子中会有更多关于这一点的微妙之处)。这些看起来像:

```
@ContextConfiguration(locations = arrayOf("classpath:/testContext.xml"))
@SpringBootTest(classes = arrayOf(Application::class), webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
internal class SomeStepDefs {} 
```

Enter fullscreen mode Exit fullscreen mode

最后，您需要一种方法来实际调用您正在运行的 Spring Boot 应用程序。有两种管理方法，取决于您是想进行 REST 调用还是 Web 浏览器。

对于 REST 调用，您需要自动连接类型为`org.springframework.boot.test.web.client.TestRestTemplate`的 bean。实际上，这是一个 RestTemplate，它被编码为知道如何调用正在运行的 Spring Boot 应用程序。它知道要到达正确的端点需要调用的基本 URL。

对于其他任何事情，您可以使用`org.springframework.boot.context.embedded.LocalServerPort`注释将一个值注入到包含 Spring Boot 应用程序正在监听的端口的类中。然后，您可以使用这个端口为任何目的编写任何 URL 例如为 Selenium 供电。