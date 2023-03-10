# 理解固体原理:开-闭原理

> 原文：<https://dev.to/theodesp/understanding-solid-principles-open-closed-principle-5h0>

[![Open closed Principle](img/d2ef30be16b8ec78a17d3e733b9d111f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--koGQSJT8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jjgmxx9g5w6dkblfsszq.jpeg)

*   ***[阅读原文](https://codeburst.io/understanding-solid-principles-open-closed-principle-e2b588b6491f)***

> 这是理解**可靠**原则系列的第 3 部分，我们将探讨什么是**开闭原则**，以及为什么它有助于创建抽象层，从而在提供新功能的同时，轻松承受不必要的变化。

作为一个小提醒，在 **SOLID** 中有五个基本原则可以帮助创建好的(或坚实的)软件架构。SOLID 是一个缩写词，其中:-

**S** 代表 SRP(单一责任原则)
**O** 代表 OCP(开闭原则)
**L** 代表 LSP(利斯科夫替代原则)
**I** 代表 ISP(接口分离原则)
**D** 代表 DIP(依赖倒置原则)

我们之前已经讨论过[依赖倒置](https://codeburst.io/understanding-solid-principles-dependency-injection-d570c15560ab)和[单一责任](https://codeburst.io/understanding-solid-principles-single-responsibility-b7c7ec0bf80)。

现在我们要讨论开闭原则。

## 开启关闭原理

> 软件实体(类、模块、函数等。)应该对扩展开放，但对修改关闭。

这背后的主要思想是，在你的代码的一部分，你有你的抽象，如类，模块，高阶函数，做一件事，并做得很好。你想让他们漂亮、有凝聚力、举止得体。

另一方面，您有不断变化的需求、范围变更、新功能请求和业务需求。你不能长时间保持你珍贵的抽象的原始形式。不可避免地，你将不得不添加更多的功能，或者添加更多的逻辑和交互。**你想有一种方法来增加额外的范围，同时保持代码良好的信誉**。

这就是开闭原则发挥作用的地方，它有助于制定一种方法来很好地做到这一点。当需要修改现有代码时，您需要确保:

*   保持当前的函数、类、模块的原样，或者至少接近它们过去的样子——也就是不可变的。

*   以可组合(避免继承)的方式扩展现有的类、函数或模块，以便它们可能以不同的名称公开新的特性或功能。

我在这里做了一个很好的笔记，试图避免经典继承，因为它倾向于以一种不好的方式耦合事物。对定义明确的结构使用经典继承，同时尽量避免误用。

更现代的方法是使用组合或复合设计模式。下面我们来看一个作文用法的例子:

```
interface IRunner {
  run: () => void;
}
class AsafaPowell implements IRunner {
  run(): void {
    console.log("9.78s");
  }
}
class Runner {
  private runnerClass: IRunner;
  constructor(runnerClass: IRunner) {
    this.runnerClass = new runnerClass();
  }
  run() {
    this.runnerClass.run();
  }
}
interface IJumper {
  jump: () => void;
}
class MikePowell implements IJumper {
  jump(): void {
    console.log("8.95,");
  }
}
class RunnerAndJumper {
  private runnerClass: IRunner;
  private jumperClass: IJumper;
  constructor(runner: IRunner, jumper: IJumper) {
    this.runnerClass = new runner();
    this.jumperClass = new jumper();
  }
  run() {
    this.runnerClass.run();
  }
  jump() {
    this.jumperClass.jump();
  }
}
const runnerAndJumper: RunnerAndJumper = new RunnerAndJumper(AsafaPowel, MikePowell)
// He can jump and he can run!
runnerAndJumper.run();
runnerAndJumper.jump(); 
```

Enter fullscreen mode Exit fullscreen mode

注意，您可以通过为构造函数参数提供一个[依赖注入容器](http://inversify.io/)来改进这个实现，以便更好地反转控制。

最棘手的部分是提前对未来的变化有一个好的想法，以便使您的抽象对变化的需求更有弹性，而不用一次又一次地重写代码。

在这种情况下，真正有帮助的是定期召开范围会议，在这里你可以提前讨论未来的项目或业务需求，以便你或你的团队在这方面提出一个系统模块的可靠设计计划。

你知道的越多越好。作为一名工程师，你可能不喜欢在冲刺过程中出现许多意外，比不得不在短时间内实现一个高效可靠的系统要多得多。你可能会抄近路。

这也意味着**我们应该总是努力编写不必在每次需求改变时都改变的代码**。通过添加新的复合类或方法，或者通过委托重用现有代码，可以添加所有新功能。

## 插件和中间件

开闭原则也适用于插件和中间件架构。在这种情况下，您的基础软件实体就是您的应用程序核心功能。对于插件来说，你有一个基础或核心模块，可以通过一个通用的网关接口插入新的特性和功能。一个很好的例子就是像 Chrome 这样的网络浏览器。

例如，在中间件的情况下，您有一个请求-响应周期，您可以在中间添加中间业务逻辑，以便为您的应用程序提供额外的服务或横切关注点。一个很好的例子就是 [Redux 中间件](https://medium.com/r/?url=http%3A%2F%2Fredux.js.org%2Fdocs%2Fadvanced%2FMiddleware.html)。

## 结论

我希望这篇文章能激励你们中的一些人应用这个原则，并从它的特点中受益。拥有编写程序的能力是一项非常强大的技术，它为扩展提供了一个定义良好的接口。关键是要有一个坚实的基础软件实体，可以在不影响当前功能的情况下进行扩展。

## 参考文献

[Wiki 页面](https://medium.com/r/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FOpen%2Fclosed_principle)
[C2 页面](https://medium.com/r/?url=http%3A%2F%2Fwiki.c2.com%2F%3FOpenClosedPrinciple)

## 接下来是理解固体原理:界面分离原理

**如果这篇文章有帮助，请分享并关注我的其他[文章](https://medium.com/@fanisdespoudis/)。你可以在 [GitHub](https://github.com/theodesp) 和 [LinkedIn](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/) 上关注我。如果你有任何想法和改进，请随时与我分享。**

快乐编码。

**如果您想安排一次指导会议，请访问我的[共同导师简介](https://www.codementor.io/theofanisdespoudis)** 。