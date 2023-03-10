# 如何创建 Guzzle6 的模拟处理程序

> 原文：<https://dev.to/hirak/how-to-create-mockhandler-of-guzzle6-893>

Guzzle 是一个 PHP HTTP 客户端库，它使得将实际发出 HTTP 请求的部分分离出来作为处理程序成为可能，通过替换它，您可以创建一个 mock。

即使您在测试时没有向实际的 API 服务器发出请求，您也可以声明您收到了想要的响应，并且可以执行代码。

# 狂饮官方嘲笑者

Guzzle 提供 MockHandler 作为测试处理程序。
[http://docs . guzzle PHP . org/en/latest/testing . html # mock-handler](http://docs.guzzlephp.org/en/latest/testing.html#mock-handler)

```
<?php
# Contents copied from the official
use GuzzleHttp\Client;
use GuzzleHttp\Handler\MockHandler;
use GuzzleHttp\HandlerStack;
use GuzzleHttp\Psr7\Response;
use GuzzleHttp\Psr7\Request;
use GuzzleHttp\Exception\RequestException;

// Create a mock and queue two responses.
$mock = new MockHandler([
    new Response(200, ['X-Foo' => 'Bar']),
    new Response(202, ['Content-Length' => 0]),
    new RequestException("Error Communicating with Server", new Request('GET', 'test'))
]);

$handler = HandlerStack::create($mock);
$client = new Client(['handler' => $handler]);

// The first request is intercepted with the first response.
echo $client->request('GET', '/')->getStatusCode();
//> 200
// The second request is intercepted with the second response.
echo $client->request('GET', '/')->getStatusCode();
//> 202 
```

Enter fullscreen mode Exit fullscreen mode

如果您像这样传递一个响应对象列表，配置的响应将从顶部开始按顺序返回。
然后换成这个`$client` (DI 什么的)，做个测试就行了。

## 做一个真正的 mock API 服务器有什么不同？

如果质量合理，创建一个真正的模拟 API 是很容易的。现在你有了`php -S`，nodejs 和 golang。那么用 Guzzle 的机制来嘲讽有什么好处呢？

### 优点

*   执行速度超快(这很自然，因为它是在同一个过程中完成的)
*   您不必考虑如何启动模拟服务器或端口号
*   开发成本低
*   你可以在启用 Guzzle 的中间件的情况下进行测试

### 弊

*   只狂饮
*   即使有直接使用 curl 或者使用 file_get_contents 的地方也不可能使用
*   因为它毕竟是 PHP，所以流的处理是非常困难的(比如非常接近地再现它流动的方式)

## 制作自己的 MockHandler

顺便说一下，你不觉得 Guzzle 的官方 MockHandler 很难用吗？由于只能从列表中按顺序返回响应，如果有许多部分命中同一个 API，你必须相应地将它多次保留在列表中。

所以，我试着让这个处理器。文档很少，但是要编写的代码量很少。

## Guzzle 处理程序概述

Guzzle 的处理程序可以是`callable`的任何东西。
然而，当它变得复杂时，建议将它变成一个实现`__invoke`方法
带 2 个参数的类。请求和两个其他选项。
返回`GuzzleHttp\Promise\PromiseInterface`。

承诺如果兑现就会通过响应对象。如果你没有抛出一个异常，而你想告诉它一个错误，通过将异常对象包装在 RejectedPromise 中来返回它。

```
<?php
use Psr\Http\Message\RequestInterface;
use GuzzleHttp\Promise;
use GuzzleHttp\Psr7;

class MyMock
{
    public function __invoke(RequestInterface $req, array $options)
    {
        return new Promise\FulfilledPromise(
            new Psr7\Response(200, ['X-Header' => 'hoge'], 'body string')
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，在上面的实现中，它是一个无论任何请求都始终返回 body string 200 的设置。

### 使用

就当`\GuzzleHttp\HandlerStack::create`的一个参数传过去。

```
<?php
use GuzzleHttp\Client;
use GuzzleHttp\HandlerStack;

$mock = new MyMock;

$handler = HandlerStack :: create ($mock);
$client = new Client (['handler' => $handler]); 
```

Enter fullscreen mode Exit fullscreen mode

除了 200 个返回之外，`$client`现在已经为任何请求做好了准备。

### 建在

处理程序接收 PSR - 7 的请求接口。如果您对此进行分析并创建动态响应的东西，您就可以更接近 home API 的行为。

例如，通过查看路径并使其动态不同。

```
<?php
//...
public function __invoke(RequestInterface $req, array $options)
{
    $path = $req->getUri()->getPath();
    switch ($path) {
        case '/foo':
            $body = 'foo!';
            break;
        case '/baa':
            $body = 'baa!';
            break;
        default:
            $body = '?!?!?!'
    }
    return new Promise\FulfilledPromise(
        new Psr7\Response(200, ['X-Header' => 'hoge'], $body)
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

或者解析主体并获取参数。

```
<?php
//...
public function __invoke(RequestInterface $req, array $options)
{
    // cast as string because it is troublesome as it is still stream (omission)
    $body = (string)$req->getBody();
    parse_str ($body, $params);
    return new Promise\FulfilledPromise(
        new Psr7\Response(200, ['Content-Type' => 'application / json'], json_encode ($params))
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以很容易地建造它。PSR 7 号可能是一个好主意，你可以用请求接口做什么。

PSR-7: HTTP 消息接口- PHP - FIG

此外，您可以为 MyMock 类设置一个合适的 setter +属性，这样就可以从外部设置响应。

## 生成错误

mock 的一个方便之处在于，如果它是一个真正的 API，很容易产生重放有点麻烦的情况。
可以返回所有错误，如连接错误和 SSL 证书错误以及 500 系列通信错误。

由于 Guzzle 的内部结构完全是 Promises / A +，所以错误通知使用的是 Promise，而不是异常机制。尽管如此，你不能抛出异常，制造异常并没有错。

只需设置 return `new RejectedPromise($e)`而不是 throw。

```
<?php
// ...
public function __invoke(RequestInterface $req, array $options)
{
    return new Promise\RejectedPromise(
        new \GuzzleHttp\Exception\ConnectException(
            'Server is down'
            $req
        )
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

这总是会导致 ConnectException。也可以根据情况自由制作。

## 观感

Guzzle 似乎是一个由中间件和处理程序组成的框架，而不是 Http 客户端。因为内部是有希望的，我在习惯如何练习上有点困难，但我认为如果你习惯了，你会做得很好。