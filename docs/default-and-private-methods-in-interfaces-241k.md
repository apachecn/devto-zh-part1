# 接口中的默认和私有方法

> 原文：<https://dev.to/lankydandev/default-and-private-methods-in-interfaces-241k>

在本教程中，我们将研究接口中的默认和私有方法。Java 8 中添加了默认方法，允许将方法添加到带有默认实现的接口中，可以使用、覆盖或忽略这些方法，而不会给已实现接口的现有类带来问题。添加默认方法时，私有方法会丢失，从而阻止代码在一个接口内被拆分成更小的方法。这对我来说有点讨厌，就好像你有一个默认的方法变得有点长，没有办法整理它。既然默认方法和私有方法都可以存在于一个接口中，我们就可以像以前一样编写方法了，尽管如果你还没有使用过默认方法，那么你首先需要忘记这样一个事实，即现在有实际的代码存在于一个接口中。

就向接口添加默认和私有方法而言，这非常简单。要添加默认方法，只需在方法定义中添加关键字`default`，我甚至不会告诉你如何添加私有方法，因为我不想侮辱你！

因为它非常简单，所以让我们直接进入一些代码示例。

```
public interface MyInterface {

  default void defaultMethod() {
    privateMethod("Hello from the default method!");
  }

  private void privateMethod(final String string) {
    System.out.println(string);
  }

  void normalMethod();
}

public class MyClassWithDefaultMethod implements MyInterface {

  @Override
  public void normalMethod() {
    System.out.println("Hello from the implemented method!");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

从上面的基本代码片段中可以看出，接口上定义了一个默认方法，该方法又调用一个私有方法。顾名思义，这是一个默认方法，因此由于`MyClassWithDefaultMethod`没有为`defaultMethod`提供实现，它将继续使用接口上定义的内容。因此，如果调用`defaultMethod`和`normalMethod`，它们将产生以下输出。

```
Hello from the default method!
Hello from the implemented method! 
```

Enter fullscreen mode Exit fullscreen mode

真的没什么。如果您想在类中提供自己的默认方法的实现，那么就像使用普通接口方法一样，只需添加一个同名的方法，最好在它上面弹出`@Override`注释。

```
public class MyClassOverrideDefaultMethod implements MyInterface {

  @Override
  public void normalMethod() {
    System.out.println("Hello from the implemented method!");
  }

  @Override
  public void defaultMethod() {
    System.out.println("I have overridden the default method!!");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

关于这段代码没有什么可说的，因为现在它看起来像一个实现了接口的普通类。运行时，它将产生以下输出，而不是之前显示的内容。

```
I have overridden the default method!!
Hello from the implemented method! 
```

Enter fullscreen mode Exit fullscreen mode

这是向接口添加默认和私有方法的基础。下面我们将更深入地研究默认方法，以及为什么首先要使用它们。

所以一个类可以有多个接口，现在一个接口可以定义它自己的默认方法。如果一个类实现了多个接口，每个接口都定义了相同的默认方法，会发生什么？实际上并没有发生什么，因为它将无法编译并产生以下错误。

```
java: class MyClassWithTwoInterfaces inherits unrelated defaults for defaultMethod() 
from types MyInterface and MyOtherInterface 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经发现，它无法确定实际应该使用哪种默认方法，所以它崩溃了。为了避免这种情况，我们需要提供一个实现来覆盖接口提供给类的版本，从而消除导致错误的不确定性。

转移到下一点。一个类可以覆盖一个默认的接口方法，并通过使用`super`调用原始方法，这与从一个扩展类调用一个超级方法非常一致。但是有一个问题，您需要在调用`super`之前输入接口的名称，即使只添加了一个接口，这也是必要的。这是有意义的，因为`super`通常指的是扩展类，并且允许多个接口，限制它总是像这样被调用，这样可以保持它的一致性，没有任何歧义。下面是代码片段，看起来会是什么样子。

```
public class MyClassWithTwoInterfaces implements MyInterface, MyOtherInterface {

  @Override
  public void normalMethod() {
    // some implementation
  }

  @Override
  public void defaultMethod() {
    MyInterface.super.defaultMethod();
    MyOtherInterface.super.defaultMethod();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

能够在默认接口方法上调用`super`也解决了来自多个接口的相同默认方法的错误。你仍然需要重写方法本身，但是你不需要写太多，如果一个原始版本满足你的需要，你可以在你想要的实现上调用`super`并完成它。

由于一个接口可以扩展另一个接口，当两个接口都包含同名的默认方法时会发生什么？我相信你们大多数人都能找到答案，但是如果你找不到，它只是从子接口/层次结构树中最底层的接口开始实现。

```
public interface MyInterface {

  default void defaultMethod() {
    System.out.println("Hello from the parent interface!");
  }
}

public interface MyOtherInterface extends MyInterface {

  default void defaultMethod() {
    System.out.println("Hello from the child interface!");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当一个类实现`MyOtherInterface`并调用`defaultMethod`时，它将打印出来。

```
Hello from the child interface! 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，它已经从子接口`MyOtherInterface`中调用了`defaultMethod`。这使它与从一个类中提供一个默认方法的实现保持一致，如果它包含在子/实现中，那么它将使用它而不是在父接口上定义的原始实现。

那么，为什么首先要使用默认方法呢？在做了一些谷歌搜索后，似乎它们被添加到 Java 8 中，作为一种添加方法的方式，为 Lambda 表达式做准备，而不破坏实现现有接口的代码。因此，如果我们有一个类使用了一个已更改的接口，例如，第三方库中的代码，我们就不需要担心任何新方法被添加到接口中，因为我们现有的代码仍然可以工作，新方法可以被忽略，直到以后。通过使用这个例子，我相信对于 API /库开发人员来说，考虑使用默认方法比那些在自己的代码库中编写代码更重要，在代码库中，他们可以控制一切，并且可以通过添加新的接口方法来更改任何被破坏的类。

下面是一些代码片段，有助于使上述观点更加清晰(如果还不清楚的话)。

```
public class MyClass implements MyOldInterface {

  @Override
  public void doStuff() {
    // does stuff
  }
}

public interface MyOldInterface {

  void doStuff();
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，这里我们有一个类，它很高兴地坐在这里，因为它已经实现了接口上定义的方法，并且不需要做任何其他工作。然后我们无忧无虑地来到这个世界，给这个接口添加了一个新的方法。

```
public interface MyNewInterface {

  void doStuff();

  void doSomeMore();
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们试图编译代码时，它会失败，因为我们没有为`doSomeMore`提供实现。显然，这让我们哭了，因为我们需要写一些额外的代码，即使是一些半 arsed 代码，以便我们可以让它再次工作。然而，如果之前的代码改为……

```
public interface MyNewInterface {

  void doStuff();

  default void doSomeMore() {
    // do some more stuff
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们会很高兴，因为我们现有的类不需要额外的工作，仍然可以编译。当我们最终决定需要实现添加到接口中的新方法时，我们可以像平常一样去做。此外，如果`doSomeMore`方法足够长，也许可以使用一两个私有方法来保持界面整洁，帮助你让使用你代码的每个人都满意！

我想这就足够了。总之，默认方法是 Java 8 的一部分，私有方法是 Java 9 的一部分。默认方法允许接口定义方法的实现，因此当类实现接口时，它不需要提供自己的方法版本，这有助于 API 和库向前发展，而不需要在接口需要额外方法时总是进行重大更改。希望这篇文章展示了它们的易用性，虽然仅仅因为它们简单并不意味着它们应该被到处添加，只关心自己代码库的开发人员可能永远不会发现使用它们的需要。我差点忘了在这里提到私有方法，但坦白地说，这是因为没有太多关于它们的内容，如果你在浏览这篇文章时注意到，几乎没有提到它们。接口中的私有方法是为了让代码看起来更好，并提供一些代码重用的选项。

如果你觉得这篇文章有帮助，请分享它，如果你想了解我的最新文章，你可以在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) 。