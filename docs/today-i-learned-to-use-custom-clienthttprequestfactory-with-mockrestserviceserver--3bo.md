# 今天我学习了:使用自定义 ClientHttpRequestFactory 和 MockRestServiceServer

> 原文：<https://dev.to/shriharshmishra/today-i-learned-to-use-custom-clienthttprequestfactory-with-mockrestserviceserver--3bo>

尽管对使用 Spring RestTemplate 编写 JAX-RS 客户端有所保留，我还是选择使用 Spring RestTemplate 来实现一个 JAX RS 客户端，因为我喜欢通过轻松模仿我正在使用的 REST 服务来编写测试用例。Spring 提供了一个非常好的工具来模仿 REST 服务端点: [MockRestServiceServer](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/client/MockRestServiceServer.html) 。然而，我遇到了一个棘手的问题。

## 问题

我使用上面的 Spring 框架工具开发了我的代码和测试用例。下一步是记录所有进出的流量。我可以通过注册一个定制的 [LoggingInterceptor](https://stackoverflow.com/a/33009822/7007521) 和[BufferingClientHttpRequestFactory](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/http/client/BufferingClientHttpRequestFactory.html)来实现这个特性。日志工作正常，但是我的测试用例开始失败。虽然我确保使用了 BufferingClientHttpRequestFactory，以便来自服务器的响应可以被读取两次——一次用于日志记录，另一次由应用程序代码读取，但是测试代码在第二次读取时会失败。

## 诊断

我发现在创建服务器时，MockRestServiceServer 会在传递给它的 RestTemplate 实例上覆盖 BufferingClientHttpRequestFactory 设置。

```
@Override
public MockRestServiceServer build(RequestExpectationManager manager) {
    MockRestServiceServer server = new MockRestServiceServer(manager);
    MockClientHttpRequestFactory factory = server.new MockClientHttpRequestFactory();
    if (this.restTemplate != null) {
        this.restTemplate.setRequestFactory(factory);
    }
    if (this.asyncRestTemplate != null) {
        this.asyncRestTemplate.setAsyncRequestFactory(factory);
    }
    return server;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 解

经过进一步搜索，我发现有一个黑客可以解决这个[问题](https://github.com/spring-projects/spring-boot/issues/6686)。一旦创建了服务器，使用 RestTemplate 实例上的 ReflectionTestUtils 读取由`MockRestServiceServer`创建的`requestFactory`实例，并用 BufferingClientHttpRequestFactory 对其进行包装。

```
@Before
public void setUp() throws Exception {
    mockServer = MockRestServiceServer.createServer(restTemplate);

    final ClientHttpRequestFactory requestFactory = 
        (ClientHttpRequestFactory) ReflectionTestUtils.getField(restTemplate, "requestFactory");

    restTemplate.setRequestFactory(new BufferingClientHttpRequestFactory(requestFactory));
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望这也能帮助你。