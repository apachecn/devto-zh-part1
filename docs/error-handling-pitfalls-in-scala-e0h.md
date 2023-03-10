# Scala 中的错误处理陷阱

> 原文：<https://dev.to/lepovirta/error-handling-pitfalls-in-scala-e0h>

Scala 中有多种错误处理策略。

错误可以表示为[异常](http://alvinalexander.com/scala/scala-try-catch-finally-syntax-examples-exceptions-wildcard)，这是 Java 等语言中处理错误的常见方式。然而，异常对于类型系统来说是不可见的，这使得它们很难处理。很容易忽略必要的错误处理，这会导致不幸的运行时错误。

在 Scala 中，通常建议将错误表示为返回类型的一部分。Scala 的标准库类型如[要么](http://danielwestheide.com/blog/2013/01/02/the-neophytes-guide-to-scala-part-7-the-either-type.html)和 [Try](http://danielwestheide.com/blog/2012/12/26/the-neophytes-guide-to-scala-part-6-error-handling-with-try.html) 可用于捕获同步操作中的错误，而 [Future](http://danielwestheide.com/blog/2013/01/09/the-neophytes-guide-to-scala-part-8-welcome-to-the-future.html) 可用于表示异步操作。此外，Scala 程序员还可以使用 [sealed trait](http://alvinalexander.com/scala/benefits-of-sealed-traits-in-scala-java-enums) 功能将不同的结果表示为自定义类型。

基于返回类型的错误处理有其自身的问题。在本文中，我将演示如何通过不小心隐藏错误场景而落入类似于`Either`和`Try`的陷阱。为了避免这个陷阱，我将描述替代的构造和代码林挺是如何减轻这些问题的。

## 意外隐藏错误的方法

除非您使用自定义类型来表示不同的结果，否则您用于捕获错误的返回类型很可能带有组合方法:`map`、`flatMap`和`withFilter`。有了这些方法，我们就可以在构建可能产生错误的程序时利用 Scala 的 for-comprehensions。

```
// Example #1
def foo(i: Int): Try[String] = ???

def bar(): Try[String] =
  for { // equivalent to foo(1).flatMap(x => foo(2).map(y => x + y))
    x <- foo(1)
    y <- foo(2)
  } yield x + y 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中(例子#1)，`foo`会产生一个错误，这个错误在`Try`返回类型中表示。当我们使用`for` / `flatMap`将操作组合在一起时，我们描述了程序在快乐路径场景中应该如何工作。如果在任何时候遇到错误值，则不执行其后的函数，错误值将作为返回值上报。使用这些组合方法，我们可以轻松地创建类似于更传统的基于异常的错误处理流程的程序，同时我们可以在返回类型中表示错误。这种连续的作曲风格被称为[一元风格](https://darrenjw.wordpress.com/2016/04/15/first-steps-with-monads-in-scala/)。

正如我们从示例程序中看到的，合成是开发人员必须完成的手动步骤。如果我们程序的某些部分不遵循组合流程，会发生什么？

```
// Example #2
def foo(i: Int): Try[String] = ???

def bar(): Try[String] = {
  val xf = foo(1)
  val yf = foo(2)
  val zf = foo(3) // unused

  for {
    x <- xf
    y <- yf
  } yield x + y
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子(例子#2)与例子#1 非常相似。两个例子组成了相同的结果，快乐路径场景也是一样的。

这些例子在运行时的工作方式有一些不同。在示例#2 中，`foo(3)`的结果被赋予名称`zf`，但是它的结果从未被使用。因为从来没有用过，所以也不会影响`bar`的结果。因此，`foo(3)`可能产生的任何错误都将完全消失。

例#2 的另一个特性是我们已经多次调用了`foo`，但是我们最多只能捕获一个在运行程序时可能发生的错误。这是因为作文遇到第一个错误总会结束。例如，如果`xf`和`yf`都包含错误，那么`bar`的结果将与`xf`相同。

在许多情况下，捕获这些错误案例是至关重要的。如果`foo`没有副作用(例如`foo`用于计算一个数的平方根)，那么函数调用只是在函数调用期间浪费了一些计算资源。然而，如果`foo`确实有副作用(例如`foo`将输入写入数据库)，我们很可能希望至少捕获它可能产生的错误，而不是让它在后台无声地失败。

这些属性之所以有问题，是因为它很容易让你陷入困境。稍微改变这些函数调用的位置，就会在如何(或是否)处理错误案例上产生微妙的差异。通常，我们希望这些例子中的两种风格在运行时能够同样工作。

这些属性不仅限于`Try`。`Either`和`Future`拥有相同的属性。

同样重要的是要强调，使用这些类型中的一些方法也会给自己带来麻烦。例如，`Try`、`Either`、`Future`都有方法 [foreach](http://www.scala-lang.org/api/current/scala/util/Try.html#foreach%5BU%5D(f:T=%3EU):Unit) ，只对成功的结果执行给定的函数，但会完全忽略失败场景:

```
def foo(i: Int): Try[String] = ???

def bar() = { // the type is Unit
  val x = foo(2)
  x.foreach(println(_))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 寻找替代方案

我们在`Either`、`Try`和`Future`身上看到的问题是，他们都被热切地评价，同时他们也能引起副作用。

这些属性放在一起似乎有问题。如果我们的计算只有这些性质中的一个会怎样？如果计算可能有副作用，但在检查之前不会对其进行评估，我们可以构建我们的计算，以便在错误处理就绪之前不会执行副作用。另一方面，如果计算不会有副作用，那么最糟糕的结果就是浪费一些 CPU 周期和内存。

让我们探索这些组合，看看我们能找到什么解决方案。

### 副作用同懒评

Scala 的`Try`值被急切地评估。让我们创建我们自己的`Try`版本，它是懒惰评估的。当值被检查时，它产生一个 Scala `Try`作为结果。

```
import scala.util.{Try, Success, Failure}

// WARNING: This is only a demo!
final class Attempt[A](proc: => A) {

  def evaluate(): Try[A] = Try(proc)

  def evaluateUnsafe(): A = proc

  def map[B](f: A => B): Attempt[B] =
    new Attempt[B](f(proc))

  def flatMap[B](f: A => Attempt[B]): Attempt[B] =
    new Attempt[B](f(proc).evaluateUnsafe())

  def withFilter(f: A => Boolean): Attempt[A] =
    new Attempt[A]({
      val r = proc
      if (f(r)) r
      else throw new NoSuchElementException("filter == false")
    })
}

object Attempt {
  def apply[A](proc: => A): Attempt[A] = new Attempt(proc)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码清单中，我们定义了自己的版本`Try`，称为`Attempt`。`Attempt`被给定一个过程作为参数，它可以产生一个值或者在求值时抛出一个异常。可以用两种方式评估该过程:安全地评估它将产生包装在标准 Scala `Try`中的过程结果，而不安全地评估它将把任何错误升级为异常。为了使`Attempt`可组合，它还定义了`map`、`flatMap`和`withFilter`方法。

让我们来看看`Attempt`的行动。我们将用执行副作用的`Attempt`定义一个程序，并验证只有流程的副作用部分会被执行。

```
def attemptPrint(s: String): Attempt[Unit] = Attempt(println(s))

def attemptOkExample: Attempt[Int] = {
  val _ = attemptPrint("I won't be printed")
  for {
    x <- Attempt(1)
    _ <- attemptPrint("x = " + x)
    y <- Attempt(2)
    _ <- attemptPrint("y = " + y)
  } yield x + y
}

attemptOkExample.evaluate() match {
  case Success(i) => println("Got: " + i)
  case Failure(ex) => println("Failed: " + ex.getMessage)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们程序的流程打印了两个数字并将它们相加。作为同一个函数调用的一部分，在流程之外，还有另一个封装在`Attempt`中的打印命令。由于`Attempt`被惰性评估，流氓打印命令根本不会被执行。我们可以从程序输出中验证这一点:

```
x = 1
y = 2
Got: 3 
```

Enter fullscreen mode Exit fullscreen mode

我们可以为 Scala `Future`创建一个类似的解决方案，这将为我们提供异步上下文中的惰性计算。我们可以利用第三方库中的现有解决方案，而不是自己实现所有功能:

*   [ScalaZ](https://github.com/scalaz/scalaz) 提供[任务](http://timperrett.com/2014/07/20/scalaz-task-the-missing-documentation/)
*   [猫](https://github.com/typelevel/cats)提供 [IO](https://github.com/typelevel/cats-effect)
*   [Monix](https://monix.io/) 提供[任务](https://monix.io/docs/2x/eval/task.html)

他们三个都是懒惰而不是渴望。ScalaZ Task 和 Cats IO 设计用于同步和异步计算，而 Monix Task 仅设计用于异步计算。

### 无副作用有急切评价

Scala 程序在代码的任何地方都有副作用。程序员可以严格遵循限制使用副作用的习语，但是编译器帮不上什么忙。然而，如果你决定遵循这个习语，Scala 的标准库类型将足以避免前面提到的问题。这里的限制当然是我们现在没有办法在我们的计算中表达效果。

### 无副作用与懒评

效果非常有用，通常我们希望在程序中至少有某种方式来表达它们。然而，将热切的评价与效果结合起来，会把我们带回到我们之前提出的问题。我们能不能建立一个系统，把效果用纯计算来表示？

早些时候，我们探索了一些懒惰和有副作用的计算解决方案。如果我们消除副作用和急切的评价会怎么样？

在这个系统中，所有的效果都可以用价值来表示，而不是副作用。然后可以将这些值组合在一起创建程序。当我们准备好执行我们的程序时，我们通过一个解释器来运行它，这个解释器把我们的值翻译成实际的副作用。

实现这种系统的一种方法是使用[自由单子](http://typelevel.org/cats/datatypes/freemonad.html)。自由单子的主题足够大，需要一篇自己的文章。要了解更多的信息，我推荐阅读[佩雷·维勒加的《自由单子概述](http://perevillega.com/understanding-free-monads)。

## “我非此即彼/尝试/未来！我该怎么办？”

不是每个项目都可以切换到其他计算类型，比如`Task`。更少的项目可以开始使用免费单子。该项目要么与现有的 Scala 类型紧密相关，要么根本无法包含其他依赖项。

不管原因是什么，如果我们能发现本文中出现的一些问题，那就更好了。了解问题并结合适当的代码审查过程会有所帮助，但是如果有一些我们可以自动化的东西就更好了。

Scala 编译器提供了一些有用的编译器标志，通过在编译过程中产生警告来帮助检测潜在的问题:

*   `-Ywarn-dead-code`帮助您检测任何无法到达的代码。
*   `-Ywarn-unused:locals` (2.12.x)和`-Ywarn-unused` (2.11.x)帮助检测函数中未使用的值。
*   `-Ywarn-value-discard`帮助检测何时未使用非单位结果。如果你真的不需要这个值，你可以显式地把它赋值给`_`来表明你想丢弃这个值。请记住，由于 [SI-7691](https://github.com/scala/bug/issues/7691) ，您不能给`_`分配多个值。为了解决这个问题，您可以创建一个函数或方法来丢弃这个值。
*   将所有警告转化为编译错误。这样，您可以在构建时强制遵守这些规则。

这些标志本身不会检测到所有问题。例如，当您组合两个已经执行的结果时，没有用于检测的标志。但是，它们对于突出一些常见问题仍然很有用。

如果你想找到更多关于编译器标志如何帮助你检测问题的信息，我推荐看看 Rob Norris 的 Scala 标志对 Scala 版本 [2.12](https://tpolecat.github.io/2017/04/25/scalac-flags.html) 和 [2.11](https://tpolecat.github.io/2014/04/11/scalac-flags.html) 的建议。除了编译器标志之外，您还可以使用诸如[wart remove](http://www.wartremover.org/)之类的工具来查找额外的代码 lint。

## 结论

在本文中，我探索了 Scala 标准库类型在错误处理方面的一些缺陷。我已经介绍了避免这些问题的替代结构以及检测这些问题的工具。

尽管 Scala 的标准库类型中肯定存在问题，但它们在许多情况下仍然非常有用。编译器标志和林挺在许多情况下可以帮助检测问题，但是更大的代码库将受益于完全避免该问题的替代构造。

本文中我没有探讨的一个领域是不遵循一元合成风格的类型中的错误处理。像 [ScalaZ Validation](https://www.47deg.com/blog/fp-for-the-average-joe-part-1-scalaz-validation/) 和 [Cats Validated](https://sihil.net/cats-validated.html) 这样的类型可以在[应用风格](https://softwaremill.com/applicative-functor/)中组合，这为你提供了聚集错误的能力。

和往常一样，我在 GitHub Gist 中发布了代码示例。感谢阅读！