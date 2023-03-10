# Spring boot 中的套接字编程

> 原文：<https://dev.to/saifali40/socket-programming-in-spring-boot-6mh>

Spring Boot 可以很容易地使独立的应用程序，你需要运行要么点击建立或运行使用 Java -jar 命令在您的终端。spring boot 附带了很多非常棒的依赖项。我想说我最喜欢的一个是 WebSocket，它减少了你的工作，不需要使用 API 来满足同样的要求，它快速有效。你只需要对你的项目做以下事情，你可以避免使用 API 进行实时消息传递或发布订阅。

将以下代码添加到您的 gradle 中

`compile("org.springframework.boot:spring-boot-starter-websocket")
compile("org.webjars:sockjs-client:1.0.2")
compile("org.webjars:stomp-websocket:2.3.3")`

这些是 spring-boot 应用程序中套接字编程的基本依赖项，现在您的环境已经准备好进行编码了。

示例配置

```
@Configuration
@EnableWebSocketMessageBroker
class WebSocketConfig extends AbstractWebSocketMessageBrokerConfigurer {

    @Override
    void configureMessageBroker(MessageBrokerRegistry config) {
        config.enableSimpleBroker("/topic")
        config.setApplicationDestinationPrefixes("/socket")
    }

    @Override
    void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/gs-guide-websocket").withSockJS()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于那些想知道我为什么不用它的人来说在我的代码中。我讨厌“；”所以我没有使用它，它也不是我在这个例子中使用 groovy 的真正答案。

现在让我们来看看控制器

```
@Controller // of course you need this 
class SocketController {
    @MessageMapping("/request")
    @SendTo("/response")
    def test() throws Exception {
        return "Hello world";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你已经完成了你的发布-订阅后端程序，让我们开始前端程序吧

`<script src="${pageContext.request.contextPath}/webjars/sockjs-client/1.0.2/sockjs.min.js"></script>
<script src="${pageContext.request.contextPath}/webjars/stomp-websocket/2.3.3/stomp.min.js"></script>`

将以下代码添加到文档就绪函数()

```
var stompClient = null;

    $(function () {
        function connect() {
            var socket = new SockJS('/gs-guide-websocket');
            stompClient = Stomp.over(socket);
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

将这段代码放在当前控件所在的位置。如果您使用 angular，请将这段代码添加到作用域中，这样您就可以使用作用域进行即时更新

发送功能

```
stompClient.send("/socket/request/");//add client desitinationprefix 
```

Enter fullscreen mode Exit fullscreen mode

响应

```
stompClient.connect({}, function (frame) {
        stompClient.subscribe('/response/', function (reponse) {
              console.log(reponse);
        });
}); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。您的套接字编程已经准备好了。