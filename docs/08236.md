# 初学者的依赖倒置原则

> 原文：<https://dev.to/scotthannen/the-dependency-inversion-principle-for-beginners-59l6>

这个原则的定义，来自罗伯特·马丁 1996 年的论文[依赖倒置原则](https://web.archive.org/web/20110714224327/http://www.objectmentor.com/resources/articles/dip.pdf)是这样的:

> A.高级模块不应该依赖于低级模块。两者都应该依赖于抽象。b .抽象不应该依赖于细节。细节应该依赖于抽象。

什么是高级模块？什么是低级模块？什么是依赖关系，如何反转依赖关系？其中一些问题将会得到解答。

依赖倒置不是一个特定于语言的概念，它明显早于。NET 框架。然而这个解释是。以网络为中心。

## 什么是依赖？

一个*依赖*是你的类所依赖或需要的东西——另一个类，或一个值。例如:

*   如果一个类需要一些设置，它可能会调用`ConfigurationManager.AppSettings["someSetting"]`。`ConfigurationManager`是*的属地*。类对`ConfigurationManager`有依赖性，多词和被动语态是一回事。但是人们说。
*   如果你写了两个类——假设是`Log`和`SqlLogWriter`——`Log`创建或需要一个`SqlLogWriter`的实例，那么`Log`依赖于`SqlLogWriter`。`SqlLogWriter`是依赖。

你知道有个词可以形容这个吗？有一段时间我没有。没有这个词，我没有意识到这两个场景有一些共同点，都描述了具有依赖关系的类。反思仅仅获得一个单词是如何影响我的感知和思考的，这让我大开眼界。

直到我看到那个基本的共性——*我写的类**依赖**其他类*——没有办法理解源于*我如何*依赖其他类的共同问题。我看到了结果:我的代码很难维护。单元测试是外来的，因为我的依赖性使我的代码不可测试，所以我主要依赖不可靠的手动测试。术语*依赖*揭示了共性，依赖倒置原则揭示了管理依赖的更好方法。

## 什么是高级模块，什么是低级模块？

定义的第一部分提到了“高级模块”和“低级模块”什么是“模块？”只是 C++和 C#在术语上的区别。在 C#中，我们编写类。C++也使用类，但这不是重点。忘记模块，想想类。

原则是“两者都应该依赖于抽象。”这意味着当应用这个原理时，高层次和低层次的区别并不重要。这些术语有一定的含义，但是我们可以应用这个原则，而不用试图将我们的类分为高层次或低层次。如果你想知道更多关于这些术语的信息，谷歌一下或者读一下罗伯特·马丁的论文。但是现在不要担心他们。我无意贬低它，但你可以降低理解这些细节的优先级。

## 什么是抽象？

原则声明我们应该“依赖抽象”抽象通常是接口、抽象类和委托(在。净条款。)我们称之为“抽象”，因为它是具体类或方法的间接表示。接口是最常用的。为了讨论方便，应用依赖反转意味着我们*依赖于接口。* ( [本文](http://blog.ploeh.dk/2010/12/02/Interfacesarenotabstractions)澄清接口是*不是*抽象，但抽象通常是接口，有时是基类。我提到它是为了承认我在概括。假设*接口*和*抽象*是同义词，即使它们不是。)

3/22/2018 - [这篇文章](https://dev.to/scotthannen/depending-on-functions-instead-of-interfaces---why-and-how-50o6)讨论了将函数或委托作为抽象来使用。

## 我们如何应用依赖倒置？

以依赖于`AppSettings` :
的类为例

```
public class ClassThatDependsOnSettings
{
    public void DoSomethingThatNeedsSettings()
    {
        var someSetting = ConfigurationManager.AppSettings["someSetting"];
        var otherSetting = ConfigurationManager.AppSettings["otherSetting"];
        CallSomeOtherMethod(someSetting, otherSetting);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个班靠`ConfigurationManager`。必须有 web.config 或 app.config，否则此类无法工作。如果同一个类依赖于抽象，那么它可能是这样的——换句话说，它依赖于一个接口，而不是依赖于`ConfigurationManager`。

一、界面:

```
public interface ISettings
{
    string SomeSetting { get; }
    int OtherSetting { get; }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后上课:

```
public class ClassThatDependsOnSettings
{
    private readonly ISettings _settings;

    public ClassThatDependsOnSettings(ISettings settings)
    {
        _settings = settings;
    }

    public void DoSomethingThatNeedsSettings()
    {
        CallSomeOtherMethod(_settings.SomeSetting, _settings.OtherSetting);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们最初的需求没有改变——我们仍然想从`AppSettings`中获取我们的值，所以我们编写了一个`ISettings`的实现，它就是这样做的:

```
public class AppSettings : ISettings
{
    public string SomeSetting { get { return ConfigurationManager.AppSettings["someSetting"]; } }
    public int OtherSetting { get { return int.Parse(ConfigurationManager.AppSettings["otherSetting"]); } }
} 
```

Enter fullscreen mode Exit fullscreen mode

第二个版本应用了依赖倒置的原则，因为它依赖于一个抽象——`ISettings`接口。这是一个不起眼的例子，只是一个开始。

## 为什么有关系？

现在我们可以很容易地用另一个实现替换`ISettings`的一个实现。我们的类不知道也不关心，因为就它而言，接口的实现无关紧要。

迟早我们会发现，我们希望一个类在一个场景中使用一个依赖项，而在另一个场景中使用不同的依赖项。也许在某些情况下，我们希望这些设置来自用户输入，而不是来自`AppSettings`。因此，我们可能会做一些事后会后悔的古怪事情，比如在构造函数或其他方法(如`useAppSettings`)中放入奇怪的参数，或者在代码中分支，或者我们创建同一个类的重复版本，或者我们试图用继承来解决问题。这三个我都做过，甚至更多。

有些人可能会因此称 YAGNI 为 T1。我们正在为可能永远都不需要的可扩展性做计划。但是如果我们打算为我们的类编写单元测试，那么我们已经需要这种灵活性了——越快越好。我们需要能够孤立地测试一个类，而不是同时测试它的所有依赖项。上面的例子很简单，因为这个类只有一个依赖项。但是如果我们的类有几个依赖项，而那些又有依赖项，等等呢？如果我们没有应用依赖反转，那么测试一个类而不测试它所依赖的每个类是不可能的。那不是真正的单元测试，并且它破坏了单元测试的一个好处，那就是通过测试*小单元代码来快速找到错误。*如果我们没有应用这个原则，那么我们可能就没有编写过单元测试，我们可能永远不会体验到用我们三分钟前编写的方法毫不费力地找到并修复一个 bug 的美妙，而不是构建整辆车，转动钥匙，从头到尾调试整个事情。编写单元测试所花的时间很快就有了回报。

即使在上面这个简单的例子中，如果我们依赖于`AppSettings`，我们如何编写单元测试来测试我们不同设置的类呢？这是不可能的，因为我们不能有一个以上版本的`AppSettings`键(除非我们进一步扭曲我们的代码来解决这个问题。)但是如果我们依赖于一个抽象(接口),那么我们可以使用一个仅为测试目的而创建的接口实现。比如:

```
public class TestScenarioOneSettings : ISettings
{
    public string SomeSetting { get { return "A"; } }
    public int OtherSetting { get { return 3; } }
} 
```

Enter fullscreen mode Exit fullscreen mode

创建这些“假”依赖关系有不同的方法。我们可以编写如上所示的简单类，有时称为 *test doubles。*像 [Moq](https://github.com/moq/moq4) 这样的工具提供了简单的方法来创建带有返回预定结果的方法和属性的接口实现。为各种语言创建和维护这种框架的多个版本强调了许多开发人员重视使用测试替身或模拟的单元测试。

最后，依赖倒置是 [SOLID](http://deviq.com/solid) 中的 D，一组已建立的、经过测试的原则，提高了我们代码的质量和可维护性。如果这是我们应用的唯一原则，我们仍然会得到一些好处。但是应用一个会导致应用其他的。我们看到一个帮助了多少，所以我们更仔细地检查另一个。这也是因为这些原则相辅相成。例如，接口分离原则改进了我们设计其他类所依赖的接口的方式。Liskov 替换原则确保我们的类可以依赖于一个接口，而无需“知道”实现是什么。

## 什么东西变反了？

你可能会发现试图想象什么被颠倒是有用或有趣的。但那只是原理的名称。原则告诉我们依赖抽象，这就是我们如何应用它。它没有告诉我们反转任何东西。

在他的论文中，在定义了依赖性反转原则之后，Robert Martin 立即声明:

> 有人可能会问我为什么要用“倒置”这个词。坦率地说，这是因为更传统的软件开发方法，例如结构化分析和设计，倾向于创建这样的软件结构，其中高级模块依赖于低级模块，抽象依赖于细节。

他建议开发人员审视他们传统的构建软件和管理依赖关系的方式，并将其颠倒过来。对我来说，这使得原则的名称(而不是原则本身)是自指的。依赖倒置是不应用依赖倒置的倒置。(他是罗伯特·马丁，他写了一篇很棒的论文，他想怎么叫就怎么叫。另外，如果他叫它别的什么，它可能不符合固体缩写。)

## 其他资源

我希望你可以谷歌一下“依赖性倒置”，作为阅读的结果，你会发现更深入的文章更容易获得。你也会看到我的例子是多么的简单。

依赖倒置之后的一些主题是[依赖注入](http://stackoverflow.com/questions/130794/what-is-dependency-injection)和[依赖注入容器，也称为 IoC(控制倒置)容器](http://stackoverflow.com/documentation/.net/5085/dependency-injection/17949/why-we-use-dependency-injection-containers-ioc-containers#t=201704280224154117753)。它与依赖倒置不同，而是一种实现和管理它的方式。(两者都有首字母“DI ”,容易引起混淆。)我的经验是，很难同时了解*如何*和*为什么*。我建议大胆一点，从如何使用 IoC 容器开始，然后再从为什么使用 IoC 容器开始。

依赖注入甚至内置于 ASP.NET 核心。在此之前，如果你想在 web 应用程序中使用 IoC 容器，你必须添加一个第三方库，比如 Castle Windsor、Unity 等等。显然，许多开发人员认为这是一种基本模式。

我还推荐阅读罗伯特·马丁博客中的所有帖子。这个博客激励着我，让我们想成为更好的开发者。我还没有像他提倡的那样采用 TDD(测试驱动开发),但是我确信我会认真尝试。

## 图表？

在这种情况下，我将断言图表没有帮助。如果某人的头脑正在努力理解一个新的原理*和*不熟悉的术语，一张图表可能只是让骆驼不知不觉超载的稻草。他们不仅不能在精神上处理图表，他们可能会变得比没有看过图表的人更难处理他们读过的东西。也许这只是我。也许我很懒，我应该更努力地跟着图表走。谷歌一下“依赖性倒置”，看看解释旁边的图表，自己判断。