# 理解坚实的原则:单一责任

> 原文：<https://dev.to/theodesp/understanding-solid-principles-single-responsibility>

[![Single Responsibility](img/7d8fa19467e9eed7338db8d7cc02eafd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--reEMcIzv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3tw0dqx7mfbaexmzsh4n.jpg)

> 这是理解**坚实**原则系列的第二部分，在这里我们探索什么是单一责任，以及为什么它有助于代码的可读性、耦合性和内聚性。

作为一个小提醒，在 **SOLID** 中有五个基本原则可以帮助创建好的(或坚实的)软件架构。SOLID 是一个缩写词，其中:-

**S** 代表 SRP(单一责任原则)
**O** 代表 OCP(开闭原则)
**L** 代表 LSP(利斯科夫替代原则)
**I** 代表 ISP(接口分离原则)
**D** 代表 DIP(依赖倒置原则)

我们之前已经讨论过[依赖注入](https://dev.to/theodesp/understanding-solid-principles-dependency-injection)，以及为什么它有助于交付松耦合和可测试的软件。

现在我们要讨论单一责任。

## 单一责任

> 每个模块或类都应该对软件提供的功能的一个部分负责，这个责任应该完全由类来封装。

或者简单地说:

> 一个类或模块应该有且只有一个被改变的原因。

好的，仅仅通过阅读它，就可以理解为什么一个类或者一个模块如果只做一件事，那么它更容易改变。为了理解整个原理，让我们专注于每个部分并对其进行分析，首先是什么是**单**，然后是什么是**责任**，然后是什么是**变化**。

## 什么是单身

> 只有一个；不是几个之一。同义词:一个，只有一个，唯一的，孤独的，孤立的，单独的；

单表示一些孤立的工作。如果你的方法，类组件做一件事，它**不做两件事**。让我们看一个例子:

```
class UserComponent { 
  getUserInfo(id) {
    this.api.getUserInfo(id).then(saveToState) // save user info to state
  }
  render() {
    const { userInfo } = this.state;
    return <div>
      <ul>
        <li>Name: { userInfo.name }</li>
        <li>Surname: { userInfo.surname }</li>
        <li>Email: { userInfo.email }</li>
      </ul>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，你可能会想，*嘿，我在我的 React 组件中一直都是这样做的！*

嗯，说实话，它可能适用于一个非常小的项目，但不适用于一个更大的项目，因为您的组件已经做了更多的工作。如果您碰巧更改了 API 服务来执行额外的逻辑，那么现在您必须修改两个或更多的地方。一个在 API 服务中，一个在组件中。这很容易升级到其他地方。

一个更好的方法是使用 props 简单地为组件提供用户信息数据，让组件只做 **1 的事情，只渲染数据**。

有各种各样的方法来辨别什么是单身，什么不是。一般来说，你需要意识到你的代码什么时候会比已经知道的多一点。

> 一个上帝的对象，也就是一个无所不知、无所不能的对象。

接下来我们来看看**责任**是什么。

## 什么是责任

责任是你的系统的每个部分，方法，类，包，模块被分配去做的工作或行为。

> 责任太大导致耦合。

关于耦合，需要理解的一点是系统的一部分对另一部分的了解程度或细节。

如果客户端代码需要知道 B 类才能使用 A 类，那么 A 和 B 被称为**耦合**。

这是不好的，因为从长远来看，这会使变化复杂化，使事情变得更糟。

瞄准合适的耦合量，以保持良好的**内聚力水平，或每个组件预期任务的度量，或组件对任务的专注程度。**

内聚性低的组件正在执行与其职责不相关的任务。例如，假设我们有一个**用户类**，我们在其中保存了一些信息。

```
class User {
  public age;  
  public name;
  public slug;
  public email;
} 
```

Enter fullscreen mode Exit fullscreen mode

只保留**设置**或**获取**角色、名称、年龄属性的方法是有意义的。

但是，如果我们决定增加一些其他的方法:

```
class User {
  public age;  
  public name;
  public slug;
  public email;
  // Xmm why do we have them here?
  checkAge();
  validateEmail();
  slugifyName();
} 
```

Enter fullscreen mode Exit fullscreen mode

那些**检查**、**验证邮件**、 **slugifyName** 的方法看起来肯定很奇怪。

这实际上会使类**不那么内聚**,因为它会分配那些在用户类中没有意义的方法。它可以在不同的类中提取，例如**用户字段验证**。

好了，现在让我们看看**变化**到底是什么。

## 什么是变化

[![Change](img/422fba8a9db4d1ea86c9c34c89135391.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iLcWC6OH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0tj172q4g8qjlxjot1n3.jpeg)

> 变更是对现有代码的更改或修改。

谁或什么是变化的来源？

> 对遗留软件系统的历史数据的研究已经确定了这种变化的三个具体原因:增加新的特性；纠正错误；和重组代码以适应未来的变化。

我知道你在想什么。我们都经历过，不是吗？假设你已经完成了一个你非常自豪的组件，因为此刻它是超级快和超级可读的。可能是你目前职业生涯中最好的一款软件。姑且称之为 **SuperDuper** 组件。

```
class SuperDuper {
  makeThingsFastAndEasy() {
    // Super readable and efficient code
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，在某个时候，您的经理要求您添加一个新特性来调用另一个类中的函数，以便它能做更多的事情。您决定在构造函数中传递它，并在您的方法中调用它。

```
class SuperDuper {
  constructor(notDuper: NotSoDuper) {
    this.notDuper = notDuper
  }
  makeThingsFastAndEasy() {
     // Super readable and efficient code
    this.notDuper.invokeSomeMethod()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个**添加新特性**的例子。您通过添加一些额外的代码行来提供一个新特性。

好了，你做了那个改变，你运行了测试服。突然你发现你破坏了 100 个测试用例。这是因为您需要在调用这个 **notDuper** 方法之前添加一个额外的检查。

```
class SuperDuper {
  constructor(notDuper: NotSoDuper) {
    this.notDuper = notDuper
  }
  makeThingsFastAndEasy() {
     // Super readable and efficient code

    if (someCondition) {
      this.notDuper.invokeSomeMethod()
    } else {
      this.callInternalMethod()
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是**纠正故障**的一个例子。您可以通过修复回归测试或处理边缘案例来修复您所做的更改。

好吧，有时你说你不喜欢这种方法，你想重构整个东西。您实现了一种不同的方式来调用 **notDuper** 方法，而不需要 ifs 使用信号分配器。

```
class SuperDuper {

  makeThingsFastAndEasy() {
     // Super readable and efficient code
     ...
     dispatcher.send(actionForTheNotDuper(payload)) // Send a signal
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个**重组代码以适应未来变化**的例子。为了在保留现有功能的同时提高可读性，您需要重新构建代码。

从这个例子中可以看出，原来的方法已经不同了，因为为了适应新的特性或错误，它已经改变了。这取决于你如何组织你的代码，使之易于改变。

## 好了，我怎么才能让我的代码遵守这个规则呢？

[![KISS](img/6e5cf3b692c21c30e7ac50d348897478.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ANxtBqtD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/482q94xy9qb7jqs1lh0g.jpg)

> 把因为同样的原因而改变的东西聚集在一起。把那些因为不同原因而改变的东西分开。

通过仔细观察构成整体的事物，并在逻辑上将它们分开，从而隔离变化。你需要了解你的代码以及为什么要这样写。总是寻找太大的方法或函数。

> 大是坏，小是好…

例如，通过检查一个构造函数是否有太多的输入参数，从而有太多的依赖关系，来跟踪依赖关系。如果可能，总是通过去离子水注射。

> 使用依赖注入

**跟踪方法参数**,因为它表示该方法可能需要在许多事情上才能发挥作用。

**使用简单的命名**,因为这将帮助你重构单一责任的代码。长的函数名暗示这里面有猫腻。

> 描述性地命名事物

尽早重构,只要你发现某些东西可以简化。这将帮助你在旅途中整理你的代码。

> 重构设计模式

最后，在重要的地方引入变化，而不是在它会使事情更耦合、更不连贯的地方引入变化，这样你的代码总是保持在那些原则之上。

> 在重要的地方引入变革。保持事情简单但不简单。

## 重述

我希望我已经帮助你理解了单一责任的含义，以及如何使你的代码更加可靠。请继续关注下一篇文章。

## 参考文献

*   [使用历史数据库确定软件变更的原因](http://research.cs.queensu.ca/~ahmed/home/teaching/CISC880/F10/papers/ReasonforChange_ICSM2000.pdf)
*   单一责任原则:伟大代码的秘诀
*   [97-事情篇](http://programmer.97things.oreilly.com/wiki/index.php/The_Single_Responsibility_Principle)
*   [s 代表单一责任](http://S%20is%20for%20Single%20Responsibility%20Principle)
*   [做一件事](https://blog.codinghorror.com/curlys-law-do-one-thing/)

## 接下来是理解固体原理:开放闭合原理

**如果这篇文章有帮助，请分享并关注我的其他[文章](https://medium.com/@fanisdespoudis/)。你可以在 [GitHub](https://github.com/theodesp) 和 [LinkedIn](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/) 上关注我。如果你有任何想法和改进，请随时与我分享。**

快乐编码。

**如果您想安排一次指导会议，请访问我的[共同导师简介](https://www.codementor.io/theofanisdespoudis)** 。

**[此处阅读原文](https://codeburst.io/understanding-solid-principles-single-responsibility-b7c7ec0bf80)**