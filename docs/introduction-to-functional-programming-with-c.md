# C#函数式编程简介

> 原文：<https://dev.to/naveen/introduction-to-functional-programming-with-c>

毫不奇怪，企业软件开发中最大的挑战之一是复杂性。变化是不可避免的。尤其是当引入了增加复杂性的新功能时。这可能会导致理解和验证软件的困难。它并没有就此停止，它增加了开发时间，引入了新的错误，并且在某些时候，如果不引入一些意想不到的行为或副作用，甚至不可能改变软件中的任何东西。这可能会导致项目变慢，并最终导致项目失败。

像面向对象编程这样的命令式编程风格有能力通过创建抽象和隐藏复杂性来将复杂性最小化到一定的水平。一个类的对象有一定的行为，不需要关心实现的复杂性就可以推理出来。正确编写的类将具有高内聚性和低耦合性，从而提高代码的可重用性，同时保持合理的复杂性。

面向对象编程在我的血液中流淌，我一生中的大部分时间都是 C#程序员，我的思维过程总是倾向于使用一系列语句来确定如何通过设计类层次结构来达到某个目标，重点关注封装、抽象和多态，这些往往会改变程序的状态，从而主动修改内存。任何数量的线程都有可能在不同步的情况下读取一个内存位置。考虑一下，如果它们中至少有一个发生了突变，导致了竞争条件。对于试图拥抱并发编程的程序员来说，这不是一个理想的条件。

然而，通过编写完整的支持并发的线程安全代码，仍然可以编写命令式程序。但是人们仍然需要对性能进行推理，这在多线程环境中是很困难的。即使并行性提高了性能，也很难将现有的顺序代码重构为并行代码，因为必须修改代码库的大部分才能显式使用线程。

## 有什么解决办法？

值得考虑“函数式编程”。这是一种编程范式，起源于比第一台计算机更早的思想，当时两位数学家引入了一种称为 lambda 演算的理论。它提供了一个理论框架，通过评估表达式而不是执行命令，并避免改变状态和突变数据，将计算视为数学函数的评估。

通过这样做，更容易理解和推理代码，最重要的是，它减少了副作用。除此之外，执行单元测试要容易得多。

**函数式语言:**
分析支持函数式编程的编程语言很有趣，比如 Lisp、Clojure、Erlang、OCaml 和 Haskell，这些语言已经被各种各样的组织用于工业和商业应用中。他们每个人都强调功能风格的不同特征和方面。ML 是一种通用的函数式编程语言，F#是 ML 语言家族的成员，起源于。Net Framework 从 2002 年开始。它将函数式编程的简洁、富于表现力和组合风格与的运行时、库、互操作性和对象模型相结合。网

值得注意的是，许多通用编程语言足够灵活，可以支持多种范例。一个很好的例子是 C#，它借鉴了 ML 和 Haskell 的许多特性，尽管它主要是强制性的，对可变状态有很大的依赖性。例如，LINQ 通过不修改它所操作的底层集合来提升声明式风格和不变性。

因为我已经熟悉了 C#，所以我喜欢组合范例，这样我就可以控制和灵活地选择最适合问题的方法。

# 基本原则:

前面已经说过，函数式编程是用数学函数编程。其思想是，每当提供相同的参数时，数学函数返回相同的结果，并且函数的签名必须传达关于它接受的可能输入和它产生的输出的所有信息。通过遵循两个简单的原则，即--------------------------------------------------------------参照透明和功能诚实。

**参照透明:**
参照透明，指的是一个函数，表示你只需要看它的参数值就可以确定应用那个函数的结果。这意味着函数应该只对我们传递的值进行操作，而不应该引用全局状态。参考下面的例子:

```
 public int CalculateElapsedDays(DateTime from)
{
     DateTime now = DateTime.Now;
     return (now - from).Days;
} 
```

这个函数不是引用透明的。为什么？今天它返回不同的输出，明天它将返回另一个输出。原因是它引用了全局日期时间。现在是财产。

这个函数可以转换成一个引用透明的函数吗？是的。
如何？

通过让函数只对我们传入的参数进行操作。

```
 public static int CalculateElapsedDays(DateTime from, DateTime now) => (now - from).Days; 
```

在上面的函数中，我们消除了对全局状态的依赖，从而使函数在引用上是透明的。

**功能诚信度:**

函数诚实声明一个数学函数应该传达关于它可能接受的输入和它可能产生的输出的所有信息。参考下面的例子:

```
 public int Divide(int numerator, int denominator)
{
   return numerator / denominator;
} 
```

这个函数是透明的吗？是的。

它有资格成为数学函数吗？可能不是。

原因:输入参数声明它接受两个整数并返回一个整数作为输出。是不是所有场景都是如此？没有。

当我们调用如下函数时会发生什么:

```
var result = Divide(1,0); 
```

是的，你猜对了。它将抛出“被零除”异常。因此，函数的签名没有传达关于操作输出的足够信息。

如何把这个函数转换成数学函数？

改变分母参数的类型，如下图

```
public static int Divide(int numerator, NonZeroInt denominator)
{
    return numerator / denominator.Value;
} 
```

NonZeroInt 是一种自定义类型，可以保存除零以外的任何整数。现在，我们已经使这个函数变得诚实，因为它传达了关于它可能接受的输入和它产生的输出的所有信息。

尽管这些原则很简单，但函数式编程需要大量的实践，这些实践对许多程序员来说可能是令人生畏的，令人不知所措的。在这篇文章中，我将尝试涵盖一些基本的方面，包括“作为一等公民的功能”，“高阶功能”和“纯功能”

## 一等公民的职能:

当函数是一等公民或一等值时，它们可以用作任何其他函数的输入或输出。它们可以赋值给变量，存储到集合中，就像其他类型的值一样。例如:

```
 Func<int, bool> isMod2 = x => x % 2 == 0;
var list = Enumerable.Range(1, 10);
var evenNumbers = list.Where(isMod2); 
```

上面的代码说明了函数确实是一级值，因为您可以将函数(x => x % 2 == 0)赋给变量 isMod2，该变量又作为参数传递给 Where(ienumbrable 的扩展)
将函数视为值对于函数式编程风格是必要的，因为它提供了定义高阶函数的能力。

## 高阶函数(HOF):

Hof 是将一个或多个函数作为参数，或者返回一个函数作为结果，或者两者兼有的函数。所有其他函数都是一阶函数。

让我们考虑同样的模 2 例子。Where”根据调用者提供的谓词进行过滤，以确定最终列表中包含哪个号码。这里提供的谓词是 isMod2 函数，IEnumberable 的 Whereextension 方法是高阶函数。实现的地方看起来像下面的

```
 public static IEnumerable<T> Where<T>
   (this IEnumerable<T> ts, Func<T, bool> predicate)
{
   foreach (T t in ts)  â¶
      if (predicate(t)) â·
         yield return t;
} 
```

遍历列表的任务是 Where 的实现细节。确定包含哪些项目的标准由致电者决定

Where HOF 将给定的函数重复应用于集合中的每个元素。Hof 也可以被设计成有条件地将函数应用于集合。我会留给你去试验。到现在为止，你应该已经意识到高阶函数是多么的简洁和强大。

## 纯函数:

纯函数是遵循我们之前讨论过的两个基本原则的数学函数——引用透明性和函数诚实性。除此之外，纯函数不会产生任何副作用。这意味着，它既不改变全局状态，也不改变输入参数。纯函数很容易测试和推理。因为输出只依赖于输入，所以求值的顺序并不重要。这些特征对于优化程序的并行化、延迟评估和记忆(缓存)非常重要。

考虑下面的控制台应用程序示例，它将一个数字列表乘以 2，并很好地将其格式化为乘法表。

```
 // Extensions.cs
public static class Extensions
{
    public static int MultiplyBy2(this int value)â¶
    {
       return value * 2;
    }
}
// MultiplicationFormatter.cs
public static class MultpilicationFormatter
{
    static int counter;

    static string Counter(int val) => $"{++counter} x 2 = {val}"; â·

    public static List<string> Format(List<int> list)
        => list
         .Select(Extensions.MultiplyBy2) â¸
         .Select(Counter) â¸
         .ToList();
}
// Program.cs
using static System.Console;
static void Main(string[] args)
{
     var list = MultpilicationFormatter.Format(Enumerable.Range(1, 10).ToList());
     foreach (var item in list)
     {
        WriteLine(item);
     }
     ReadLine();
}
// Output
1 x 2 = 2
2 x 2 = 4
3 x 2 = 6
4 x 2 = 8
5 x 2 = 10
6 x 2 = 12
7 x 2 = 14
8 x 2 = 16
9 x 2 = 18
10 x 2 = 20 
```

一个纯函数
一个不纯函数，因为它改变了全局状态
纯函数和不纯函数可以类似地应用

上面的代码执行得很好，没有任何问题，因为我们只对 10 个数字进行操作。如果我们想要对更大的数据集执行相同的操作，尤其是在处理 CPU 密集型数据时，该怎么办？并行执行乘法有意义吗？这意味着数据序列可以独立处理。

当然，我们可以使用并行 LINQ (PLINQ)的力量来获得几乎免费的并行化。如何才能实现这一点？只需在列表中添加一个平行项。

```
 public static List<string> Format(List<int> list)
=> list.AsParallel()
         .Select(Extensions.MultiplyBy2)
         .Select(Counter)
         .ToList(); 
```

但是等等，纯函数和不纯函数不能很好地并行化。

我这么说是什么意思？如你所知，计数器函数是一个不纯函数。如果你有一些多线程方面的经验，你会很熟悉这一点。并行版本将有多个线程读取和更新计数器，并且没有锁定。程序最终会丢失一些更新，导致不正确的计数器结果，如下图

```
 1 x 2 = 2
2 x 2 = 4
7 x 2 = 6
8 x 2 = 8
6 x 2 = 10
4 x 2 = 12
9 x 2 = 14
10 x 2 = 16
5 x 2 = 18
3 x 2 = 20 
```

避免状态突变:
解决这个问题的一个可能方法是避免状态突变和运行计数器。我们能想出一个解决方案来生成我们需要的计数器列表，并从给定的项目列表中映射它们吗？我们来看看如何:

```
 using static System.Linq.ParallelEnumerable;
public static class MultpilicationFormatter
{
   public static List<string> Format(List<int> list)
   => list.AsParallel()â¶
      .Select(Extensions.MultiplyBy2)
      .Zip(Range(1,list.Count), (val, counter) => $"{counter} x 2 = {val}")â·
      .ToList();
} 
```

使用 Zip 和 Range，我们重写了乘法格式化程序。Zip 可以用作扩展方法，因此可以使用更流畅的语法编写 Format 方法。经过这一变化，Format 方法现在是纯粹的了。让它平行只是蛋糕上的一颗樱桃。除了-和-之外，这几乎与顺序版本相同

当然，事情并不总是像这个简单的场景那么简单。但是到目前为止，您所看到的想法将使您能够更好地处理与并行性和并发性相关的问题。

这篇[文章](https://medium.com/@naveenrtr/introduction-to-functional-programming-with-c-b167f15221e1)最初发表在我的[媒体博客](https://medium.com/@naveenrtr)