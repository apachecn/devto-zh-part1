# 你嘲笑错了。

> 原文：<https://dev.to/asizikov/you-are-mocking-it-wrong-5gh3>

嗯，可能你不是，但是让我发一点牢骚。

[![Mockingbird picture](img/f157eb30cc11b0b653406f8e517beb3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bXcdoQ0M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://asizikov.github.img/2017-12-mocking/mockingbird.jpg) 
*知更鸟懂得嘲弄。*

在我的职业生涯中，我一直在与各种代码库打交道，有一种模式是我经常看到的。你可能已经猜到了我在这里要谈论的是单元测试和嘲讽。为了给它一个好的吸引人的开始，我在这里声明，模仿应该在你不得不做的时候使用，而不是在你可以做的时候使用。

我举几个有点没用甚至有害的测试的例子。所有的例子都是虚构的，但我希望你能抓住要点。

所以，让我们从典型的`Greeter`例子开始。这是一个单元测试的“你好，世界”。以这样或那样的方式，这个例子在所有致力于单元测试和模仿框架的文章、帖子和书籍中重复出现。

```
IGreeter{
   string Greet(string name, string title);
}

public class Greeter: IGreeter{
  public string Greet(string name, string title){
    return "Hello," + title + " " + name;
  }
}

public class Client{
  private IGreeter _greeter;
  public Client(IGreeter greeter){
    _greeter = greeter;
  }

  public string FormatPageHeader(string name, string title){
    return "<h>" +  _greeter(name, title) + "</h>";
  }  
}

 public class ClientTests {
    pubic void Test(){
        var mock = new Mock<IGreeter>();
        mock.Setup(greeter => greeter.Greet("John", "Mr.")).Returns("Hello, Mr. John");
        var result = new Client(mock.Object).FormatPageHeader("John", "Mr.");
        Assert.AreEquals(result, "<h>Hello, Mr. John</h>"); //All good here, what does it test, tho?
    } 
 } 
```

到目前为止一切顺利。测试是绿色的。不过，欢迎界面并不完美。两个`strings`作为参数？太容易搞砸了，不是吗？您的项目中很可能有这样的方法。

好吧，那么，想象你决定使这个方法不容易出错。你没有太多的时间来进行适当的重构，因为你有一个特性要处理。让我们交换参数。对于一个人来说，把头衔放在名字前面要自然得多。我知道这取决于文化。

(“约翰”、“先生”)比(“先生”、“约翰”)和(“博士”、“史密斯”)更别扭。

所以，我们将以这样的欢迎方式结束:

```
//Version 2
public class Greeter2 : IGreeter{
  public string Greet(string title, string name){
    return "Hello, " + title + " " + name;
  }
} 

IGreeter{
   string Greet(string title, string name);
} 
```

`add.`、`commit`、`push`。我们亲爱的构建服务器将会选择变更，运行测试，当然也会失败。我们忘记了更新对`IGreeter`实现的测试。一旦他们修好了，我们就没事了，不是吗？

也不是，还记得`Client`课吗？现在这是不正确的，我们仍然按照旧的顺序传递标题和名称，尽管我们的测试声称一切正常。

[![This is fine](img/ecbeff50a487c7e3674b8d4fb8d1942d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qMaZSadv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://asizikov.github.img/2017-12-mocking/fine.jpg)

这是一个悖论，我们引入了 mock，这样我们可以孤立地测试我们的类，但是我们必须放一些代码来使 mock 返回一些东西。还是那个逻辑，不是吗？现在`IGreeter`至少有两个实现。很可能在你的代码库中，有几十个被模仿类的实现。仅仅因为它是一个非常重用的依赖项，而且你必须从头再来一遍。

我们可以改进测试，调整它，但是唯一能让它失败的方法是在我们的模拟设置中重复`Greeter`逻辑。但是等一下，如果我们有相同的实现，为什么不重用现有的代码呢？

您的模拟对象越复杂，您的模拟设置就变得越复杂。如果不是这种情况，很可能你只是在测试与你愚蠢的模仿者的交互，也就是说，你没有测试任何东西。就像上面的`ClientTest`一样。唯一积极的结果是，你的公司为你在构建服务器上节省的 CPU 时间向亚马逊支付了额外的 20c。

拥有一个以上的依赖关系并不罕见。我可以想象我们的`Client`可以使用一些`IHmlRenderer`来消耗`IGreeter`结果。你也会嘲笑这个，对吗？

甜蜜如。您现在有一个测试来验证两个模拟是如何相互集成的。这如何证明你的代码是正确的呢？我不知道。

我现在退一步，从另一个角度来谈。典型的系统是什么样的？我认为，它不是一堆孤立的类，而是更像一个依赖关系的蜘蛛网。如果你看类型依赖图，你会看到一组集群，每个集群都是一组紧密耦合的类。这就是我们管理复杂性的方式，我们将一个大类分解成小块，但是这些小块永远不会被孤立使用。他们每个人将负责一小部分工作。您将为它们编写一个单独的测试套件，模拟出所有的依赖项(它们都属于同一个集群)。

这与尝试[测试私有方法](https://stackoverflow.com/questions/34571/how-do-i-test-a-private-function-or-a-class-that-has-private-methods-fields-or)有何不同？

标准库类的那个小小的方便包装器只不过是一个实现细节。

### 但是集成测试很慢...

这是意料之中的事。如果我们回到我们的`ClientTest`。使用 mock 的测试和使用具体的欢迎实现的测试哪个性能更好？我想答案很明显。我们不应该忘记那个测试不能证明任何事情。它更慢，它分配更多，它是错误的。我认为这是有害的。

### 所以你每次考试都要发那些邮件？

好的，这是个好问题。记得我说过当我们不得不嘲笑别人的时候，而不是当我们 T2 可以嘲笑别人的时候吗？这正是测试隔离的正确情况。

你不想在运行单元测试套件时进行 HTTP 调用，你不想发送电子邮件，我也不想。

### 嘿，我试着不去嘲笑，我厌倦了所有的依赖

这就是痛苦的地方。我见过这样的系统，维护起来简直是一场噩梦。手动设置依赖项的依赖性真的不是办法。这很难，每个人都会避免编写新的测试。但这是一个很容易解决的任务。

我们如何在运行时构建依赖树？我希望你使用的是将接口和实现连接在一起的 DI 框架，它知道如何创建一个新的依赖关系，它知道何时以及如何重建一个依赖关系树。如果您正在构建一个 web 服务，您应该重置请求之间的状态，所以尽可能地保持请求范围内的依赖关系。

同样的模式也适用于您的测试套件。一切都是请求范围内的，并且已经是无状态的，所以将每个测试视为一个“请求”,让 DI 容器为您构建测试中的系统。

当然，您必须以不同的方式设置 DI 容器。

模块(或集群)级测试将为该模块构建依赖关系树，但会模拟世界的其他部分。就像没有 HTTP 呼叫，没有数据库，没有电子邮件，你已经得到了要点。

此时，您会意识到您不需要验证您的实用程序类可以拆分字符串的测试，但是您会确信您的`MortgageCalculator`完成了这项工作。毕竟，这是您的业务规则，也是您正在构建的功能。当然，除非你是底层框架开发人员。然而，我们大多数人都不是。

一旦你测试好了所有的集群，建立了所有的接口，你就可以试着把它分解一点。或者你可能想让它保持原样，这取决于你。

假设您想要将一些逻辑提取到单独的类中。现在您的系统有了一个新的依赖项，但是这个逻辑已经被测试过了。您不需要设置新的 mock，不需要在测试设置中重复提取的类的逻辑，不需要更新测试来实例化被测试的类。它就在那里，你的安全网仍然有效。如果您提取并修改逻辑，您将会破坏测试。你看到美了吗？提取类并引入新的依赖项就像将逻辑移入私有方法一样简单。

### 总结

上述方法将为您带来以下好处:

*   更少有意义、无用和有害的测试
*   不需要维护重复的实现(模拟设置)
*   功能驱动测试，验证集群完成工作的测试
*   易于重构的代码库
*   您可以改进模块级访问(不需要为了测试而将类公开)

根据我的经验，基于集群方法的测试要可靠得多。当它们中断时，它们实际上意味着系统功能失调，当它们通过时，你可以确定你没有中断逻辑。

我们仍然必须运行集成测试，以确保第三方集成正常工作，系统的运行时配置有效。

我对任何批评都持开放态度，可以随意撕毁这篇文章。:)

*交叉发布自我的[个人博客](https://asizikov.github.io/2017/12/23/you-are-mocking-it-wrong/)*