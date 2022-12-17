# 理解坚实的原则:依赖倒置

> 原文：<https://dev.to/theodesp/understanding-solid-principles-dependency-injection>

这是理解坚实原则系列的第一部分，在这里我们探索什么是依赖倒置，以及为什么它有助于交付松耦合和可测试的软件。

当阅读书籍或与其他开发人员交谈时，您可能会遇到或听到术语 **SOLID** 。

在这种讨论中，有些人提到了它的重要性，以及理想的系统需要具备那些**原则**的所有特征。

当在日常场景中工作时，您可能没有时间去思考架构，或者如何在不违反时间限制或老板意见的情况下整合好的设计决策。

然而，这些原则不仅仅是为了跳过它们。软件工程师应该将它们应用到他们的开发工作中。每次你输入代码时，真正的问题是你如何正确地应用这些原则，从而使你的代码更加优雅。

在 SOLID 中，有五个基本原则有助于创建好的(或可靠的)软件架构。SOLID 是一个缩写词，其中:-

**S** 代表 SRP(单一责任原则)
**O** 代表 OCP(开闭原则)
**L** 代表 LSP(利斯科夫替代原则)
**I** 代表 ISP(接口分离原则)
**D** 代表 DIP(依赖倒置原则)

这些原则最初是由 Robert C. Martin 在 20 世纪 90 年代编制的，它们提供了一种构建软件组件的方法，这些软件组件是紧密耦合的代码，具有较差的内聚性和很少的封装性；松散耦合的代码，内聚并真正恰当地封装了业务的实际需求。

> 好的实践是减少耦合，提高可测试性、可维护性和可替换性。

[![testability](img/f9f8c29912f0d0c1e0a24f83f4531455.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s9WtqiSE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/33frow44kvmbi3z4iz24.jpg)

这是很重要的一点。原则不会把一个糟糕的程序员变成一个好的程序员。原则必须用判断来应用。如果它们被武断地应用，就如同它们根本没有被应用一样糟糕。

> 这不再仅仅是关于设计模式了。它是关于对每个领域问题的彻底评估，并运用实用的解决方案来避免关键的代码味道。

原则和模式的知识让你有理由决定何时何地应用它们。尽管这些原则主要是启发性的，但它们是解决常见问题的常识性解决方案。**在实践中，它们被一次又一次地证明**。因此，应该用常识来对待它们。

在本文的剩余部分，我将开始探索**依赖倒置原则**。

## 依存倒置。

> A.高级模块不应该依赖于低级模块。两者都应该依赖于抽象。抽象不应该依赖于细节。细节应该依赖于抽象。

这告诉你什么？一方面，你有**抽象**。在软件工程和计算机科学中，抽象是一种安排计算机系统复杂性的技术。它的工作原理是建立一个人与系统交互的复杂级别，将更复杂的细节抑制在当前级别之下。它的范围很广，涵盖了不止一个子系统。

> 因此，当使用抽象时，您是在系统的高层次视图上工作。你只关心你能做的交互，而不关心如何做。

另一方面，你有**低级模块或者细节**。那些是相反的。它们是为解决特定问题而编写的程序。它们的范围是有限的，通常覆盖一个单元或一个子系统。例如，打开一个 MySQL 数据库连接被认为是低级的，因为它被绑定到一个特定的范围。

现在，通过读出这两条规则，你能暗示什么？

依赖反转背后的真正意图是[将](https://en.wikipedia.org/wiki/Coupling_%28computer_programming%29)对象去耦合到这样的程度，即不需要仅仅因为它所依赖的对象需要被改变为不同的对象而改变客户端代码。这实现了**的松散耦合**，因为它的每个组件都很少或者根本不知道其他独立组件的定义。它实现了**可测试性**和**可替换性**，因为松耦合系统中的组件可以被提供相同服务的替代实现所替换。

依赖反转的缺点是需要一个**依赖注入容器**，需要配置。这个容器将具有所需的能力，以正确的范围和正确的参数将您的服务注入到所需的位置。

## 太棒了！那么我该如何开始呢？

在实践中，我们可以使用 Javascript 中一个名为 [Inversify.js](http://inversify.io/) 的令人敬畏的库来学习更多关于依赖倒置的知识。

> Inversify.js .这是一个强大的轻量级反转控制容器，用于由 TypeScript 支持的 JavaScript & Node.js 应用程序。

在这个例子中，我将向您展示如何为一个 **WebSocket** 连接提供服务。

比方说，您有一个 web 服务器，它公开了 WebSockets 和想要连接到服务器并接收更新的客户端。目前，有几种提供普通 WebSocket 的解决方案。有 **Socket.io** ， **Socks** ，一个普通的 **WebSocket** 等等……它们中的每一个都有不同的 API 或者你可以调用的不同方法。如果我们以某种方式在一个公共接口中抽象出 WebSocket 提供者的整个概念，这是不是很好？这样，我们可以提供一个不同的 WebSocket 创建器或 **SocketFactory** ，它接受某种配置并根据我们的需要创建 WebSocket 连接。

首先，让我们定义我们的接口:

```
export interface WebSocketConfiguration {
  uri: string;
  options?: Object;
}

export interface SocketFactory {
  createSocket(configuration: WebSocketConfiguration): any;
} 
```

注意，这里没有具体的东西，只有接口。我们说那些是我们的**抽象**。

现在，假设我们想要一个 Socket-io 工厂:

```
import {Manager} from 'socket.io-client';

﻿class SocketIOFactory implements SocketFactory {
  createSocket(configuration: WebSocketConfiguration): any {
    return new Manager(configuration.uri, configuration.opts);
  }
} 
```

这是一些具体的东西，不再是抽象的，因为它从 Socket-io 库中指定了一个管理器。那就是我们的**细节**。

我们可以添加更多的工厂类，只要它们实现 SocketFactory 接口。

现在我们有了自己的工厂，我们需要一种方法来提供一个不依赖于特定实例的抽象。

让我们从一个新的抽象开始:

```
export interface SocketClient {
  connect(configuration: WebSocketConfiguration): Promise<any>;
  close(): Promise<any>;
  emit(event: string, ...args: any[]): Promise<any>;
  on(event: string, fn: Function): Promise<any>;
} 
```

让我们提供抽象的详细视图:

```
class WebSocketClient implements SocketClient {
  private socketFactory: SocketFactory;
  private socket: any;

  public constructor(webSocketFactory: SocketFactory) {
    this.socketFactory = webSocketFactory;
  }

  public connect(config: WebSocketConfiguration): Promise<any> {
    if (!this.socket) {
      this.socket = this.socketFactory.createSocket(config);
    }

    return new Promise<any>((resolve, reject) => {
      this.socket.on('connect', () => resolve());
      this.socket.on('connect_error', (error: Error) => reject(error));
    });
  }

  public emit(event: string, ...args: any[]): Promise<any> {
    return new Promise<string | Object>((resolve, reject) => {
      if (!this.socket) {
        return reject('No socket connection.');
      }

      return this.socket.emit(event, args, (response: any) => {
        if (response.error) {
          return reject(response.error);
        }

        return resolve();
      });
    });
  }

  public on(event: string, fn: Function): Promise<any> {
    return new Promise<any>((resolve, reject) => {
      if (!this.socket) {
        return reject('No socket connection.');
      }

      this.socket.on(event, fn);
      resolve();
    });
  }

  public close(): Promise<any> {
    return new Promise<any>((resolve) => {
      this.socket.close(() => {
        this.socket = null;
        resolve();
      });
    });
  }
} 
```

注意我们如何将 SocketFactory 类型的参数传递给构造函数。这是依赖性倒置的第一条规则。对于第二条规则，我们需要一种方法来提供这个值，它既容易替换，也容易配置，而不需要了解它的细节。

这就是 Inversify 介入并管理这种控制的原因。让我们添加一些注释:

```
import {injectable} from 'inversify';

const webSocketFactoryType: symbol = Symbol('WebSocketFactory');
const webSocketClientType: symbol = Symbol('WebSocketClient');

let TYPES: any = {
    WebSocketFactory: webSocketFactoryType,
    WebSocketClient: webSocketClientType
};

@injectable()
class SocketIOFactory implements SocketFactory {...}

...

@injectable()
class WebSocketClient implements SocketClient {

public constructor(@inject(TYPES.WebSocketFactory) webSocketFactory: SocketFactory) {
  this.socketFactory = webSocketFactory;
} 
```

这些注释只是添加了关于如何在运行时提供所有这些组件的额外元数据。现在需要做的只是创建我们的依赖倒置容器，并以正确的类型绑定所有东西。

```
import {Container} from 'inversify';
import 'reflect-metadata';
import {TYPES, SocketClient, SocketFactory, SocketIOFactory, WebSocketClient} from '@web/app';

const provider = new Container({defaultScope: 'Singleton'});

// Bindings
provider.bind<SocketClient>(TYPES.WebSocketClient).to(WebSocketClient);
provider.bind<SocketFactory>(TYPES.WebSocketFactory).to(SocketIOFactory);

export default provider; 
```

就是这样！现在，每当您需要 SocketClient 的实例时，只需调用具有正确绑定的容器。

```
var socketClient = provider.get<SocketClient>(TYPES.WebSocketClient); 
```

当然，Inversify 提供的不仅仅是简单的绑定。我建议你去网站上看看。

## 习题

1 找到其他提供依赖倒置容器的库。

1.  在示例中，我确实使用“Singleton”范围实例化了容器。如果我不指定，会发生什么？使用 Inversify 还有什么其他方法可以做到这一点？

2.  查看您自己的项目或网站，考虑如何在您的服务中利用依赖倒置。API 调用、承诺等示例。

3.  **额外学分**:实现自己的 DI 容器库。拥有一个接受键和值的类或数据结构。对于键，您指定一个名称，对于值，您指定已解析的实例。尝试添加指定范围的方法，比如作为单例或工厂。

## 重述

我希望你了解 DI 是什么，并让你意识到它的特点。请继续关注下一篇文章。

## 参考文献

[扎实的原则](https://scotch.io/bar-talk/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)
[维基](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)
[扎扎实实的开始](https://sites.google.com/site/unclebobconsultingllc/getting-a-solid-start)
[另一个维基](http://wiki.c2.com/?DependencyInversionPrinciple)
[从笨到实](http://williamdurand.fr/2013/07/30/from-stupid-to-solid-code/)

## 接下来是理解坚实的原则:单一责任

**如果这篇文章有帮助，请分享并关注我的其他[文章](https://medium.com/@fanisdespoudis/)。你可以在 [GitHub](https://github.com/theodesp) 和 [LinkedIn](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/) 上关注我。如果你有任何想法和改进，请随时与我分享。**

快乐编码。

*奥古斯丁·劳塔罗在 Unsplash 上拍摄的封面照片*