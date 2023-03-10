# 比较 Spring 的 RestTemplate 和 Jersey 的客户端 API

> 原文：<https://dev.to/shriharshmishra/comparing-springs-resttemplate-and-jerseys-client-apis-350>

[![Comapring Spring's RestTemplate and Jersey's Client APIs](img/fdafeb7a24f48a9ae58fac27dea08704.png "Comapring Spring's RestTemplate and Jersey's Client APIs")](https://res.cloudinary.com/practicaldev/image/fetch/s--CFYhbQp4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6l86g9pdpwyr7n0ct4v2.png) 
最近我有机会使用两个不同的 REST 库来编写使用 REST web 服务的客户端代码。这些库是 Spring RestTemplate (spring-web，4.3.10.RELEASE)和 Jersey Client (jersey-client，1.18)。虽然后者是一个旧版本，但它更容易使用。我的目标非常简单，具体如下:

*   在服务上调用不同的 HTTP 方法
*   在客户端代码上设置基本身份验证。
*   记录请求和响应。
*   处理 HTTP 错误响应。
*   编写模拟服务器的测试用例。

Spring 提供了 [RestTemplate API](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html) 来调用 REST Web 服务上的各种 HTTP 方法。虽然有专门用于大多数 HTTP 方法的方法，如 [getForObject](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#getForObject-java.lang.String-java.lang.Class-java.lang.Object...-) GET、 [postForObject](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#postForObject-java.lang.String-java.lang.Object-java.lang.Class-java.lang.Object...-) POST、PUT 等。，这些方法不能完全控制要发送的内容。所以你不得不依靠通用的方法比如[交换](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#exchange-java.lang.String-org.springframework.http.HttpMethod-org.springframework.http.HttpEntity-java.lang.Class-java.lang.Object...-)或者[执行](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#execute-java.lang.String-org.springframework.http.HttpMethod-org.springframework.web.client.RequestCallback-org.springframework.web.client.ResponseExtractor-java.lang.Object...-)。当使用这些方法时，即使您不想发送任何参数，也必须提供诸如 HttpEntity 之类的参数，这个模糊命名的类用于设置请求的 HTTP 头(这个[线程](https://stackoverflow.com/questions/11579621/spring-resttemplate-postforobject-with-header-webservice-cant-find-my-header-p)上的一些例子)。

该 API 提供了使用简单的路径参数和 HTTP 参数模板动态构建 URI 的方法。然而，这种方法很笨拙，因为有两种方法可以在 URI 模板上实现数据绑定。一种是使用一张地图来替换 URI 模板中的键。第二种方法是按照占位符出现在 URI 模板上的顺序将 var 参数传递给方法。这两种方法都要求在 URI 模板中提到 HTTP 参数名。这两种方法都可能导致代码混乱，很难跟踪哪个参数设置了哪个值。你可以在这个上看到例子。另一方面，Jersey client 提供了一个 [fluent API](https://en.wikipedia.org/wiki/Fluent_interface) 来实现客户端代码。该 API 非常直观，不需要任何特定的顺序来为 URI 模板提供数据。使用 [WebResource](http://javadox.com/com.sun.jersey/jersey-bundle/1.18/com/sun/jersey/api/client/WebResource.html) API 上的方法可以很容易地提供查询和路径参数。在第[页](http://buraktas.com/jersey-client-example-with-json-support/)上有更多的例子。

使用其中一个库设置基本身份验证并不困难。Spring 要求我们在 RestTemplate 实例上设置一个拦截器，而 Jersey 要求添加过滤器类。这两个库都提供了内置的实现，可以根据需要进行扩展。Spring 提供了[BasicAuthorizationInterceptor](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/http/client/support/BasicAuthorizationInterceptor.html)，Jersey 提供了 [HTTPBasicAuthFilter](https://github.com/jersey/jersey-1.x/blob/master/jersey-client/src/main/java/com/sun/jersey/api/client/filter/HTTPBasicAuthFilter.java) 。

记录请求和响应是调查的一个重要特征。像基本认证一样，这个特性在 Spring 中使用一个拦截器，在 Jersey 中使用一个过滤器来实现。Jersey 提供了一个非常方便的实现，比如叫做 [LoggingFilter](https://github.com/jersey/jersey-1.x/blob/master/jersey-server/src/main/java/com/sun/jersey/api/container/filter/LoggingFilter.java) 的过滤器，它可以帮助记录所有类型的传入和传出流量。Spring 要求你实现自己的拦截器。但是这还不够，您还需要配置 RestTemplate 实例来使用[BufferingClientRequestFactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/BufferingClientHttpRequestFactory.html)，这有助于从输入流读取两次响应——一次用于日志记录，另一次用于应用程序代码。示例实现可以在这里看到[。Jersey 中 LoggingFilter 的示例实现内置了这个方面。所以新泽西给了我们一个先发制人的机会。](https://stackoverflow.com/a/22620168)

在我看来，Spring RestTemplate 处理不在 2xx 范围内的 HTTP 状态代码的方法不是用户友好的。如果没有看到成功的响应，Spring 将抛出一个异常-[HttpClientErrorException](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/HttpClientErrorException.html)或[httpserverrorexception](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/HttpServerErrorException.html)。这迫使开发人员实现一个 try-catch 块来处理错误或向 RestTemplate 注册一个错误处理程序。而 Jersey 的方法是移交 [ClientResponse](http://javadox.com/com.sun.jersey/jersey-client/1.18.3/com/sun/jersey/api/client/ClientResponse.html) 实例，由开发者决定如何处理响应，即更多的自由。

最后，让我们开始编写测试用例。这里至关重要的第一件事是模拟服务器响应。春天显然是这里的赢家。它提供了一组非常方便的 API 来模拟 REST web 服务响应，这使我们可以专注于测试业务逻辑。看一下 [MockRestServiceServer](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/client/MockRestServiceServer.html) 。对于 Jersey，你必须依靠 Mockito 这样的模拟 API 来模拟 Jersey 客户端 API，这非常令人分心和无聊。

考虑到我发现使用 Jersey 编写使用 REST web 服务的代码是多么容易，我会说相比之下 Jersey 赢了。Spring 框架足够开放，为我们提供了库，让我们可以在使用 Spring 的同时使用 Jersey。因此，即使您正在开发基于 Spring 的应用程序，您也可以安全地使用 Jersey 来满足您的 JAX-RS 需求。

声明:我写这篇博客是为了分享我在一个相当简单的用例中的经验。所以 YMMV。喜欢春天的人可能不同意我的观点。还有其他的库，比如 RESTEasy，我还没有探索过。你可以分享你的经验/观点，这样我也可以从你的经验中学习。