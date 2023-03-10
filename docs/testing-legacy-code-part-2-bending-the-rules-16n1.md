# 测试遗留代码，第 2 部分:变通规则

> 原文：<https://dev.to/rapasoft/testing-legacy-code-part-2-bending-the-rules-16n1>

所以，你决定给难看的代码添加一些测试。[您已经准备好想要测试的测试场景](https://dev.to/rapasoft/testing-legacy-code-part-1-how-to-start-2km7)。您开始编写端到端的测试，来验证特定的功能。

有一个很好的机会，你会找到这样的东西:

*   你想替换一些依赖，但是它不是注入的，而是使用构造函数在代码中创建的，例如`SomeService someService = new SomeService();`。
*   您想要更改通过网络调用外部服务的方法调用的结果，但它是一个静态方法，例如`SomeService.fetchExternalResult(...)`
*   任何违反你一直努力坚持的设计原则的事情

## 你能做什么，但可能不应该做什么

做一个简单的重构。用依赖注入替换手动对象实例化或静态方法调用。听起来很简单，对吧？嗯，通常是这样，但是我想说明一个可能发生的问题。

我一直在处理一些神秘的代码，我们决定将一些简单的 POJO 类转移到另一个包中。等级制度看起来不太对，这看起来真的是很简单的改变。然而，当我们这样做时，一些操作在运行时开始失败。经过一些认真的调查，我们发现，一些外部(但在内部)的依赖是检查特定的包名，然后在“一个特殊的方式”使用。它做了什么并不重要，重要的是这样一个简单的改变引起了几个小时的调查。像这样的事情很难预测，尤其是当反射被误用的时候。

因此，一定要小心行事，不要在没有研究可能的后果之前就开始修改代码。

## 大概有什么更好的办法...

...但是仍然不理想。对于那些特殊情况，可以使用字节码操作或反射。准备测试，验证功能，然后进行重构。不要手动操作，你应该使用一些库，例如我们已经为 Mockito 使用了 [PowerMock](https://github.com/powermock/powermock/wiki) 扩展。

为什么我认为它不理想？嗯，首先 PowerMock 基本上是一个*黑客*工具。如果您的代码中已经有一些字节码操作，您很可能需要调整您的设置。 [JaCoCo](https://github.com/powermock/powermock/wiki/Code-coverage-with-JaCoCo) 代码覆盖工具有一个持续的问题。在你开始使用它之前，你应该先做一个调查。

PowerMock 和类似的工具也迫使你依赖于实现细节，这将导致**你的测试与产品代码**紧密耦合。这意味着，每次重构产品代码时，您都必须重构测试。

因此，你应该只在没有其他办法的时候使用它。

## 什么是最不痛苦的方式

正如我在第一部分提到的，你应该测试最不模糊的黑盒。这意味着，您将让实现保持原样，并进行一些组件/集成测试，这些测试将使用输入参数来创建备选场景。

您很有可能需要模仿外部依赖性。这些是驻留在您的测试基础设施中的一些服务器上的，是共享的，并且很难更改。幸运的是，对于我们开发人员来说，几乎所有东西都有替代品。以下是一些例子:

*   对于 HTTP 通信(REST APIs 等。)可以使用 [WireMock](http://wiremock.org/) 。它是一个高度灵活的工具，可以在项目内部使用，也可以作为外部服务器使用。您可以模拟外部服务器的响应，也可以设置可以验证的预期。如果您的项目没有任何提供类似功能的应用程序框架，这将特别有帮助。
*   对于更复杂的场景，可以使用 [Maven Cargo 插件](https://codehaus-cargo.github.io/cargo/Maven2+Plugin+Reference+Guide.html)。有时候，创建一个单独的应用程序来模拟你的呼叫更容易。如果 WireMock 还不够，你可以使用一个快速原型框架，比如 [Spring Boot](https://projects.spring.io/spring-boot/) 。我们已经在我们的一个重构项目中成功地使用了这一点，我们希望用同时开发的 REST API 替换数据库访问层。我们的模拟服务器是一个简单的接口实现，它只是访问数据库。
*   如果数据库访问有问题，你可以使用一些测试数据库库的内存实例，比如 [H2](http://www.h2database.com/html/main.html) 。当生产代码使用特定的方言时，问题就出现了(是的，我正在看着你，Oracle)。
*   对于大多数非 SQL 数据库，存在一个嵌入式实例或测试实例，可以用作替代。您还可以找到[有用的测试库](https://github.com/jsevellec/cassandra-unit)，它们将执行测试数据准备和验证。

## 底线

当然，有许多方法，几乎所有的方法都是基于对所有可能性的仔细调查。这总是取决于您的项目设置。我真的对其他解决方案感兴趣，因此如果你能分享你自己的经验，我会非常感激。