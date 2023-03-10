# 关于 Scala 和它的标准库，我不知道的是

> 原文：<https://dev.to/frosnerd/what-i-did-not-know-about-scala-and-its-standard-library-401>

## 简介

我使用 Scala 作为我的主要编程语言已经有三年了。为了好玩，我最近做了一些基本的 [Scala 语言练习](https://www.scala-exercises.org/std_lib),发现了一些我不知道的特性和可能性。

一些我觉得非常有用的东西。其他人我不确定我是否喜欢他们。在这篇博文中，我们将讨论

1.  从地图中移除元组
2.  永无止境的旅行
3.  部分功能域
4.  反勾号的不同用法
5.  中缀类型
6.  采掘性企业

让我们一个一个来看看。我没有按照特定的顺序排列它们，所以你可以随意跳过一个或者浏览直到你找到你感兴趣的东西！

## 1。从地图中移除元组

“从地图中删除元组？听起来很容易，你怎么不知道怎么做？”我同意。听起来很容易。在 Scala 中，你可以使用`-`方法通过键从 map 中移除元素。

```
Map(1 -> "a", 2 -> "b") - 1 == Map(2 -> "b") 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们考虑一个地图，其中的关键是一对。

```
Map((1, 2) -> "a", (2, 3) -> "b") - (1, 2) == Map((2, 3) -> "b") 
```

Enter fullscreen mode Exit fullscreen mode

```
error: type mismatch;
 found   : Int(1)
 required: (Int, Int)
       Map((1, 2) -> 2, (2, 3) -> 3) - (1, 2)
                                        ^
error: type mismatch;
 found   : Int(2)
 required: (Int, Int)
       Map((1, 2) -> 2, (2, 3) -> 3) - (1, 2)     
                                           ^ 
```

Enter fullscreen mode Exit fullscreen mode

哎呦！这是怎么回事？原来不止有一个`-`方法，而是有两个:

```
def -(elem: A) = ???

def -(elem1: A, elem2: A, elems: A*) =
  this - elem1 - elem2 -- elems 
```

Enter fullscreen mode Exit fullscreen mode

这个方法允许你一次移除多个键，类似于 [`--`](http://www.scala-lang.org/api/2.12.3/scala/collection/Map.html#--(xs:scala.collection.GenTraversableOnce%5BA%5D):Repr) ，但是用 [varargs](http://daily-scala.blogspot.de/2009/11/varargs.html) 。为了让我们上面的例子工作，我们需要添加一对额外的括号。

```
Map((1, 2) -> "a", (2, 3) -> "b") - ((1, 2)) == Map((2, 3) -> "b") 
```

Enter fullscreen mode Exit fullscreen mode

我不知道为什么这个方法存在，为什么它被称为`-`而不是`--`，因为它清楚地一次移除多个元素。但是我确信，当使用元组作为键时，这会导致混乱。

## 2。永无止境的旅行

在 Scala 中，每个集合都是一个 [`Traversable`](http://docs.scala-lang.org/overviews/collections/trait-traversable.html) 。可移动对象有不同的操作，例如添加它们(`++`)、转换它们的元素(`map`、`flatMap`、`collect`等等。它们也给你提供了获取尺寸信息的方法(`isEmpty`、`nonEmpty`、`size`)。

当询问可遍历对象的大小时，您希望得到与集合中元素数量相对应的答案，对吗？`List(1, 2, 3).size`应该是`3`，因为列表中有三个元素。但是`Stream.from(1).size`呢？流是一种可能没有确定大小的可遍历对象。实际上这个方法永远不会返回。它将永远继续穿越。

幸运的是，有一个叫做`hasDefiniteSize`的方法告诉你在你的可遍历性上调用`size`是否安全，例如`Stream.from(1).hasDefiniteSize`将返回`false`。请记住，如果这个方法返回`true`，集合肯定是有限的，但是不保证反过来:

*   `Stream.from(1, 2).take(5).hasDefiniteSize`返回`false`，但是
*   `Stream.from(1).take(5).size`就是`5`。

我不经常使用内置的`Stream`类型，如果我使用了，我知道里面是什么，如果不合适就不调用`size`。但是如果你想提供一个接受任何`Traversable`的 API，在试图遍历到末尾之前，一定要检查它是否有一个确定的大小。

## 3。部分功能域

在函数式编程中，你把你的程序看作是数学函数的组合。函数是从输入到输出的纯的、无副作用的转换。

然而，给定大多数编程语言中可用的标准类型(例如，整数、浮点、列表等。)并不是每个方法都是函数。如果你将两个整数相除，如果除数为 0，实际上没有定义。

Scala 通过使用 [`PartialFunction`](http://www.scala-lang.org/api/2.12.3/scala/PartialFunction.html) 为您提供了一种表达方式，表明该函数不是 [total](https://en.wikipedia.org/wiki/Total_relation) (从数学上来说，这使得它不是一个函数，而只是一个关系，因为根据定义，函数需要是 total)。注意，Scala 并没有真正告诉你像`Int./`和`List.head`这样的方法是部分函数。

您可以直接定义一个`PartialFunction`，也可以使用 case 语句:

```
val devide2 = new PartialFunction[Int, Int] {
  override def isDefinedAt(x: Int): Boolean = x != 0
  override def apply(x: Int): Int = 2 / x
}

val divide5: PartialFunction[Int, Int] = { case i if i != 0 => 5 / i } 
```

Enter fullscreen mode Exit fullscreen mode

我之前不知道的是，你需要使用这个`isDefinedAt`方法来检查这个函数是否可以应用到你的输入参数中。

```
devide2.isDefinedAt(3) == true
devide5.isDefinedAt(0) == false 
```

Enter fullscreen mode Exit fullscreen mode

我们如何处理没有为输入定义函数的情况？

*   首先，尝试修复您的域名。如果你正在处理一个列表，并且你想安全地调用`head`，因为你想确保接收一个非空列表，只接受[类型非空列表](https://typelevel.org/cats/datatypes/oneand.html)的输入。这与我在上一篇博文中讨论的关于[选择正确的数据模型使无效状态成为不可能](https://dev.to/frosnerd/making-the-invalid-impossible---choosing-the-right-data-model-9e6)有很大关系。让编译器为您工作！
*   如果你不能修复你的域名，你可以尝试修复你的部分功能。不是定义除法为`(Int, Int) -> Int`，而是定义为`(Int, Int) -> Option[Int]`，在除数为 0 的情况下返回`None`。现在您不再拥有部分功能。如果是`head`，你可以用`headOption`来代替。
*   如果不想触及您的部分功能，您可以将它与其他部分功能结合起来，以覆盖整个领域。您可以使用`orElse`组合两个部分功能。如果第一个部分函数不能应用，Scala 将尝试使用第二个。

## 4。反勾号的不同用法

到目前为止，我只在需要使用保留关键字作为变量名时才使用反勾号，例如，当使用像`Thread.yield`这样的 Java 方法时。但是在使用`case`语句时，它还有另一个用例。

当在一个`case`语句中进行模式匹配时，以小写字母开始的情况是局部绑定的变量名。以大写字母开头的大小写用于直接匹配变量名。

```
val A = "a"
val b = "b"

"a" match {
  case A => println("A")
  case b => println("b")
}
// prints 'A'

"b" match {
  case A => println("A")
  case b => println("b")
}
// prints 'b'

"c" match {
  case A => println("A")
  case b => println("b")
}
// prints 'b' 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们可以看到在最后一个例子中`case b`也匹配`"c"`，因为`b`是一个局部绑定变量，而不是之前定义的`val b`。如果你想在`val b`上匹配，你可以把它重命名为`val B`，或者我之前不知道的，把它放在箱子里的反勾里。

```
val A = "a"
val b = "b"

"b" match {
  case A => println("A")
  case `b` => println("b")
}
// prints 'b'

"c" match {
  case A => println("A")
  case `b` => println("b")
  case _ => println("_")
}
// prints '_' 
```

Enter fullscreen mode Exit fullscreen mode

## 5。中缀类型

在 Scala 中，类型参数可以用来表达[参数多态性](https://en.wikipedia.org/wiki/Parametric_polymorphism)，例如在[泛型类](https://docs.scala-lang.org/tour/generic-classes.html)中。这提供了一种抽象的方式，允许我们实现一次可以在不同输入类型上工作的功能。

如果你的类有多个类型参数，你可以用逗号分隔它们。假设我们想要实现一个保存一对任意值的类。

```
case class Pair[A, B](a: A, b: B) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像这样创建新对:

```
val p: Pair[String, Int] = Pair("Frank", 28) 
```

Enter fullscreen mode Exit fullscreen mode

然而，用中缀类型的符号，你也可以写:

```
val p: String Pair Int = Pair("Frank", 28) 
```

Enter fullscreen mode Exit fullscreen mode

我知道 Scala 想成为一种可伸缩、灵活、可扩展的语言。但是在我看来，给开发者太多的方法去做或表达同样的事情会让他们很难读懂别人的代码。

如果你幸运的话，不同的风格意味着你在看一个新项目的源代码时必须习惯这种风格。如果你不是，那么在一个项目中，表达同一事物的不同方式混杂在一起，使得阅读和理解正在发生的事情变得困难。

我看到在[用例](https://stackoverflow.com/questions/33347955/real-life-examples-of-scala-infix-types)中，中缀类型符号很方便，但是也很容易使代码完全不可读。谁会认为 [`String ==>> Double`](https://oss.sonatype.org/service/local/repositories/releases/archive/org/scalaz/scalaz_2.11/7.1.4/scalaz_2.11-7.1.4-javadoc.jar/!/index.html#scalaz.%24eq%24eq%24greater%24greater) 是作为平衡二叉树实现的键/值对的不可变映射类型？

## 6。采掘性企业

为了模式匹配一个对象，它需要有一个`unapply`方法。使用这种方法的对象被称为[提取器对象](https://docs.scala-lang.org/tour/extractor-objects.html)。

定义 case 类时，编译器会自动为您生成一个提取器对象，以便您可以利用模式匹配。然而，也可以直接定义`unapply`:

```
object Person {
  def apply(internalId: String, name: String) = s"$internalId/$name"

  def unapply(idAndName: String): Option[String] =
    idAndName.split("/").lastOption
}

val p = Person(java.util.UUID.randomUUID.toString, "Carl")
p match {
  case Person("Carl") => println("Hi Carl!")
  case _ => println("Who are you?")
}
// prints 'Hi Carl!' 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利。我不知道的是，类的实例也可以用来提取:

```
class NameExtractor(prefix: String) {
  def unapply(name: String): Option[String] =
    if (name.startsWith(prefix)) Some(name) else None
}

val e = new NameExtractor("Alex")
"Alexa" match {
  case e(name) => println(s"Hi $name!")
  case _ => println("I prefer other names!")
} 
```

Enter fullscreen mode Exit fullscreen mode

这允许您自定义提取器对象。

## 结论

通过 [Scala 练习](https://www.scala-exercises.org/std_lib)非常有趣，虽然我已经知道了大部分主题，但发现一些新东西也很令人兴奋。我认为，即使你认为自己是前辈、专家、大师、摇滚明星或其他什么人，总有你不知道的事情，时不时重温一下基本知识也无妨。

你对我们在这篇文章中讨论的事情有什么看法？你觉得带 varargs 的`-`方法有用吗？你有没有尝试过计算一个无限可穿越空间的大小，因为你没有检查它是否有一个确定的大小？你知道在调用一个部分函数之前，你需要检查它是否被定义了吗？你在代码中经常使用反勾号吗？你有没有定义过你自己的类型，它应该用在中缀符号中？还是在没有意识到的情况下使用了中缀符号中的类型？你能想出一个现实世界中提取器类而不是对象的用例吗？

请在下面的评论中告诉我你的想法！