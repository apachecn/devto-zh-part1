# 为什么作为共享代码的方式，组合优于继承

> 原文：<https://dev.to/ruidfigueiredo/why-composition-is-superior-to-inheritance-as-a-way-of-sharing-code>

继承是面向对象编程(OOP)的支柱之一，另外两个是多态和封装。因此，无论何时讨论 OOP，继承都是中心问题。然而，遗传有几种不同的味道。有实现继承和接口继承。本文的重点是前者。

实现继承被描述为实现代码重用的一种方式。这是通过在一个类中定义方法和变量，然后由其他类继承来实现的。那些其他类被认为是从原始类派生的，而原始类被认为是它们的基类。

派生类可以访问基类的方法和变量，这就是代码重用的实现方式。

以这种方式共享代码的想法可以追溯到 1967 年，特别是 SIMULA 语言，它被认为是面向对象编程诞生的原因。

这个概念可以追溯到近 50 年前，它一定是一个伟大的想法，因为它今天仍在使用。但是把这个想法放在一边，想想这个。与我们今天所做的相比，那时编写的程序的规模如何？那时我还没有出生，但是我从经历过的人那里听说，按照当时的标准，500 行代码是一个非常大的程序。今天，这已经很少了。

还有，那个时候人工智能做的还不错(这是在 [AI 寒冬](https://en.wikipedia.org/wiki/History_of_artificial_intelligence#The_first_AI_winter_1974.E2.80.931980)之前)，有 IS-A 这个概念非常流行。IS-A 是一个来自知识推理领域的概念，即语义网。在一个[语义网中](https://en.wikipedia.org/wiki/Semantic_network)有节点和弧线。节点代表一个实体，弧线代表实体之间的关系。一种类型的弧被命名为 IS-A，其中来自 IS-A 关系的源节点的[属性被目标节点](http://web.onetel.net.uk/~hibou/ai-course/lec03.txt)继承。听起来很熟悉？

这种想法必须来自某个地方，尽管它在知识表示和推理领域非常有用，您可以根据它进行推理(例如，猫是哺乳动物，所有哺乳动物都有皮毛，所以猫也有皮毛)，但它在 OOP 中并不那么有用。事实上，我听过的对这个概念应用于 OOP 的最好描述来自于鲍勃叔叔...继承不是 ISA。继承，如果你用一种非常厌倦的眼光来看，继承是子作用域中方法和变量的声明，它与 ISA 无关……”

在描述如何使用继承时，IS-A 的这种思想今天仍然很流行。只需[搜索“面向对象编程继承”并选择图片](https://www.google.co.uk/search?q=object+oriented+programming+inheritance&tbm=isch)。

那么为什么实现继承不是代码重用的好工具呢？嗯，它很大程度上基于另一个领域的想法，而这些想法在 OOP 中并不适用，而且还有更好的选择。我说的当然是作曲。

首先，让我们看看通过继承的代码重用是什么样子，然后看看通过组合的代码重用是什么样子。

```
public class Base 
{
    public void SharedMethod(){
        //do something
    }   
}

public class MyClass : Base
{
    public void Method()
    {
        //SharedMethod is available here
    }   
} 
```

第一件显而易见的事情是，我们想要重用的代码只对派生类可用，而且大多数现代语言只允许每个类有一个基类。

这个设计是不透明的。我这样说的意思是，通过查看派生类，你所拥有的关于共享方法的唯一信息就是它的名字。通常，您需要遍历层次结构才能理解正在发生的事情。

此外，如果派生类有多行代码，就很难区分哪些方法是在基类中定义的，哪些是在派生类中定义的，因为没有什么可以区分它们。

最后，这不是一个测试友好的设计。假设`SharedMethod`调用数据库，您想要为`MyClass`类编写单元测试。嘲笑调用`SharedMethod`的结果是一件麻烦事。

使用合成会是什么样子呢？

```
public interface IDependency 
{
    void SharedMethod();
}

public class Depedency : IDependency 
{
    public void SharedMethod()
    {
        //a shared method's implemetation
    }
}

public class MyClass    
{
    private readonly IDependency _dependency;

    public MyClass(IDependency dependency)
    {
        _dependency = dependency;
    }

    public void Method()
    {
        _dependency.SharedMethod();
    }   
} 
```

现在，任何类都可以使用我们想要重用的代码，他们只需要引用包含我们想要共享的代码的依赖项。

对我们人类来说,`MyClass`中有更多的信息。这不仅仅是调用我们正在重用的没有任何上下文的方法。用`IMailService`代替`IDependency`，用`SendEmail`代替共享方法，它做什么和在哪里实现就变得很明显了。

你可能已经注意到我使用了一个接口`IDependency`而不是一个具体的类。这是组合实现的另一件事，当通过实现继承共享代码时，这是不可能的。当您使用实现继承时，您将派生类绑定到基类中的特定实现。

如果你想为`MyClass`编写单元测试，那么使用任何[隔离框架](http://www.blinkingcaret.com/2012/11/20/interaction-testing-fakes-mocks-and-stubs/)来模拟对`IDependency`的调用并隔离测试`MyClass`的方法是非常容易的。

我几乎想把实现继承称为自动复制和粘贴，如果你仔细想想，它最纯粹的形式就是这样。

尽管这是一种不太灵活的代码共享方式，但它为什么如此受欢迎呢？它的授课方式非常吸引人，尤其是 IS-A 概念乍一看似乎很有道理。我想这就是它流行的原因，可能是大多数开发人员想要共享代码时首先想到的。

然而，当你仔细观察实现继承作为共享代码的工具时，你会很快发现它是次等的，这就是为什么可能有这么多的来源说你应该更喜欢组合而不是继承。这是另一个，有一点历史。