# Scala 中模式匹配泛型类型的方法

> 原文：<https://dev.to/lepovirta/ways-to-pattern-match-generic-types-in-scala-56dc>

在 Scala 中，偶尔需要在类型信息丢失的地方对值进行模式匹配。然而，当您想要提取的值包含类型参数或者本身就是泛型类型时，解决方案就不再那么简单了。

例如，如果知道要提取的值的具体类型，就很容易想出解决方案:

```
def extractString(a: Any): Option[String] = a match {
  case s: String => Some(s)
  case _ => None
} 
```

Enter fullscreen mode Exit fullscreen mode

但是当你试图匹配一个泛型类型时，JVM 中的[类型擦除会阻止你在运行时执行比较。这是因为泛型只存在于编译阶段，而不存在于运行时。Scala 的泛型类型也是如此，这就是为什么在运行时`List[String]`实际上只是一个`List`。因为不管它们的类型参数是什么，所有的列表都有相同的类型，所以仅仅通过运行时类型信息来区分`List[String]`和`List[Int]`是不可能的。](https://docs.oracle.com/javase/tutorial/java/generics/erasure.html)

在本文中，我演示了一些针对泛型类型进行模式匹配时可以使用的解决方案。首先，我将演示两种完全避免这个问题的方法。之后，我将展示如何使用[不成形的](https://github.com/milessabin/shapeless)特征来解决这个问题。最后，我将展示如何使用 Scala 的[类型标签](http://docs.scala-lang.org/overviews/reflection/typetags-manifests.html)来解决这个问题。

简要内容:

1.  [避免丢失通用类型](#avoid-losing-the-generic-type)
2.  [避免泛型类型参数的匹配](#avoid-matching-on-generic-type-parameters)
3.  [引入可类型化和类型化案例](#introducing-typeable-and-typecase)
4.  [类型标签](#type-tags)
5.  [结论](#conclusions)

## 避免丢失类属类型

在您尝试对泛型类型进行模式匹配之前，请尝试确定您是否真的需要它。如果您对需要模式匹配的代码有控制权，请尝试以不丢失泛型类型信息的方式构建代码。下面是一个在模式匹配中会遇到问题的结构的例子:

```
sealed trait Result
case class Ok[T](values: List[T]) extends Result
case class Fail(message: String) extends Result

def handleResult(result: Result): Unit = result match {
  case Fail(message) => println("ERROR: " + message)
  case Ok(vs: List[String]) => println("Got strings of total length: " + vs.map(_.size).sum)
  case _ => println("Got something else")
}

handleResult(Fail("something happened")) // output: "ERROR: something happened"
handleResult(Ok(List("this", "works")))  // output: "Got strings of total length: 9
hanldeResult(Ok(List("doesn't", "work", 4))) // ClassCastException... oops 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，请注意我们在运行时尝试传入混合类型对象列表时得到的`ClassCastException`。像这样的异常在实时系统中可能是致命的，但是很容易写错。理想情况下，我们可以验证这些类型的错误是在编译时而不是运行时被捕获的。

因为`Result`类型没有对`Ok`类型的类型参数施加任何限制，所以类型参数信息丢失了。我们可以给`Result`类型添加类型参数，以便更好地管理类型:

```
sealed trait Result[+T]
case class Ok[+T](values: List[T]) extends Result[T]
case class Fail(message: String) extends Result[Nothing]

def handleResult(result: Result[String]): Unit = result match {
  case Fail(message) => println("ERROR: " + message)
  case Ok(vs) => println("Got strings of total length: " + vs.map(_.size).sum)
}

handleResult(Fail("something happened")) // output: "ERROR: something happened"
handleResult(Ok(List("this", "works")))  // output: "Got strings of total length: 9
handleResult(Ok(List("doesn't", "work", 4))) // compile time error 
```

Enter fullscreen mode Exit fullscreen mode

既然类型参数包含在`Result`类型中，我们可以在编译时对包含在`Ok`类型中的列表类型施加边界。这让我们避免完全重新获得泛型参数。

使类型参数协变允许我们将`Fail`值传递给`handleResult`函数。`Result`中的协方差允许将类型为`Result[T]`的值传递给`handleResult`函数，其中`T`是`String`的子类型。因为`Nothing`是所有其他类型(包括`String`)的子类型，所以`Fail`值——属于`Result[Nothing]`类型——可以作为参数传递给`handleResult`。这允许我们保持类型如`Fail`类型无参数。

## 避免在泛型类型参数上匹配

有时，您无法控制将什么类型的值传递给正在实现的函数。例如， [Akka 框架](https://github.com/akka/akka)中的参与者被迫处理所有类型(类型`Any`)的消息。由于您不能在编译时给传入的值添加边界，您将再次失去区分`List[String]`和`List[Int]`的能力。

```
def handle(a: Any): Unit = a match {
  case vs: List[String] => println("strings: " + vs.map(_.size).sum)
  case vs: List[Int]    => println("ints: " + vs.sum)
  case _ =>
}

handle(List("hello", "world")) // output: "strings: 10"
handle(List(1, 2, 3))          // ClassCastException... oh no! 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，如果我们试图将一个整数列表传递给`handle`函数，该函数将试图将该列表解释为一个字符串列表，当我们试图以字符串列表的形式访问该列表时，该列表将以`ClassCastException`结束。如前一节所述，我们希望在编译期间清除这些意外的失败案例。

如果您可以控制传递到函数中的值的类型(例如，您可以控制发送给参与者的消息的类型)，那么您可以通过用指定类型参数的容器将具有类型参数的输入打包来避免这个问题:

```
case class Strings(values: List[String])
case class Ints(values: List[Int])

def handle(a: Any): Unit = a match {
  case Strings(vs) => println("strings: " + vs.map(_.size).sum)
  case Ints(vs)    => println("ints: " + vs.sum)
  case _ =>
}

handle(Strings(List("hello", "world"))) // output: "strings: 10"
handle(Ints(List(1, 2, 3)))             // output: "ints: 6"
handle(Strings(List("foo", "bar", 4)))  // compile time error 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们定义了具体的类型`Strings`和`Ints`来管理通用类型。它们确保您不能构建混合值的列表，因此`handle`函数可以安全地使用它们的列表，而不必在运行时知道列表的类型参数。

## 引入可类型化和类型化案例

[无形状](https://github.com/milessabin/shapeless)提供了处理类型安全铸造的便捷工具:`Typeable`和`TypeCase`。

Typeable 是一个类型类，它提供了将值从`Any`类型转换为特定类型的能力。转换操作的结果是一个`Option`，其中`Some`值将包含成功转换的值，而`None`值表示转换失败。Shapeless 为所有 Scala 的基本类型、case 类、密封的 traits 层次结构以及至少一些 Scala 的集合类型和基本类提供了开箱即用的可类型化功能。下面是一些可打字的例子:

```
import shapeless._

case class Person(name: String, age: Int, wage: Double)

val stringTypeable = Typeable[String]
val personTypeable = Typeable[Person]

stringTypeable.cast("foo": Any) // result: Some("foo")
stringTypeable.cast(1: Any)     // result: None
personTypeable.cast(Person("John", 40, 30000.0): Any) // result Some(...)
personTypeable.cast("John": Any) // result: None 
```

Enter fullscreen mode Exit fullscreen mode

TypeCase 是可类型化和模式匹配的桥梁。它本质上是一个针对`Typeable`实例的[提取器](http://danielwestheide.com/blog/2012/11/21/the-neophytes-guide-to-scala-part-1-extractors.html)。TypeCase 和 Typeable 允许在不装箱的情况下实现上一节中的示例:

```
import shapeless._

val stringList = TypeCase[List[String]]
val intList    = TypeCase[List[Int]]

def handle(a: Any): Unit = a match {
  case stringList(vs) => println("strings: " + vs.map(_.size).sum)
  case intList(vs)    => println("ints: " + vs.sum)
  case _ =>
}

val ints: List[Int] = Nil

handle(List("hello", "world")) // output: "strings: 10" so far so good
handle(List(1, 2, 3))          // output: "ints: 6" yay!
handle(ints)                   // output: "strings: 0" wait... what? We'll get back to this. 
```

Enter fullscreen mode Exit fullscreen mode

除了装箱列表值，`TypeCase`实例可以用于输入的模式匹配。TypeCase 将自动使用它能为给定类型找到的任何`Typeable`实例来执行转换操作。如果铸造操作失败(产生`None`，图案不匹配，尝试下一个图案。

### 保持类属类型“类属”

虽然 Typeable 可用于特定类型的模式匹配，但它真正强大的功能是能够对提取类型的类型参数保持泛型的类型进行模式匹配。下面是一个使用这种能力的例子:

```
import shapeless._

def extractCollection[T: Typeable](a: Any): Option[Iterable[T]] = {
  val list = TypeCase[List[T]]
  val set  = TypeCase[Set[T]]
  a match {
    case list(l) => Some(l)
    case set(s)  => Some(s)
    case _       => None
  }
}

val l1: Any = List(1, 2, 3)
val l2: Any = List[Int]()
val s:  Any = Set(1, 2, 3)

extractCollection[Int](l1)    // Some(List(1, 2, 3))
extractCollection[Int](s)     // Some(Set(1, 2, 3))
extractCollection[String](l1) // None
extractCollection[String](s)  // None
extractCollection[String](l2) // Some(List()) // Shouldn't this be None? We'll get back to this. 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们已经创建了函数`extractCollection`来提取任何通用类型的所有列表和集合。我们声明当我们调用函数时，函数类型参数`T`应该有一个`Typeable`实例。实例的存在允许我们为一列`T`和一组`T`创建提取器。然后我们可以使用这些提取器只提取符合类型`List[T]`或`Set[T]`的值。对于所有其他的价值，我们不创造价值。

为了使用这个函数，我们需要提示编译器我们想要提取什么类型的值。这是通过将提取值的类型指定为函数的类型参数来实现的。

通过保持提取的类型是通用的，我们已经成功地将部分提取逻辑从我们想要提取的类型中分离出来。这允许我们对所有有`Typeable`实例的类型重用相同的函数。

### Typeable 的秘制酱

虽然 Typeable 看起来有解决类型擦除的能力，但它仍然与任何其他运行时代码具有相同的行为。这可以在前面的代码示例的最后几行看到，其中空列表被识别为字符串列表，即使它们被指定为整数列表。这是因为可类型转换是基于列表值的。如果这个列表是空的，那么它自然是一个有效的字符串列表和一个有效的整数列表(或者任何其他的列表)。根据不同的用例，不同类型的空列表之间的区别可能重要，也可能不重要，但是如果用户不熟悉 Typeable 如何操作，这肯定会让他们措手不及。

此外，由于 Typeable 检查集合的值来确定该集合是否可以转换，因此转换大集合比转换小集合需要更长的时间。让我们做一些基本的分析，看看集合的大小如何影响造型。

```
import shapeless._

def time[T](f: => T): T = {
  val t0 = System.currentTimeMillis()
  val result = f
  val t1 = System.currentTimeMillis()
  println(s"Elapsed time: ${t1 - t0} ms")
  result
}

val list1 = (1 to 100).toList
val list2 = (1 to 1000).toList
val list3 = (1 to 10000).toList
val list4 = (1 to 100000).toList
val list5 = (1 to 1000000).toList
val list6 = (1 to 10000000).toList

val listTypeable = Typeable[List[Int]]
time { listTypeable.cast(list1: Any) } // 0 ms
time { listTypeable.cast(list2: Any) } // 1 ms
time { listTypeable.cast(list3: Any) } // 5 ms
time { listTypeable.cast(list4: Any) } // 4 ms
time { listTypeable.cast(list5: Any) } // 7 ms
time { listTypeable.cast(list6: Any) } // 70 ms 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们创建了不同大小的列表，并测量了转换它们需要多长时间。请注意，随着集合规模的扩大，所需的时间会增加。

### 自定义可类型化的类型类实例

有时候，您可能会遇到无法自动对其使用 Typeable 的类型。这些通常是具有类型参数的标准类(与 case 类相对)。例如，您不能在以下类型上自动使用 type able:

```
class Funky[A, B](val foo: A, val bar: B) {
  override def toString: String = s"Funky($foo, $bar)"
} 
```

Enter fullscreen mode Exit fullscreen mode

相反，我们必须提供我们自己的定制`Typeable`实例，以允许转换`Funky`值。`Typeable`接口要求我们实现两个方法:`cast`和`describe`。`cast`方法完成真正的类型转换工作，而`describe`提供关于被转换类型的可读信息。

```
implicit def funkyIsTypeable[A: Typeable, B: Typeable]: Typeable[Funky[A, B]] =
  new Typeable[Funky[A, B]] {
    private val typA = Typeable[A]
    private val typB = Typeable[B]

    def cast(t: Any): Option[Funky[A, B]] = {
      if (t == null) None
      else if (t.isInstanceOf[Funky[_, _]]) {
        val o = t.asInstanceOf[Funky[_, _]]
        for {
          _ <- typA.cast(o.foo)
          _ <- typB.cast(o.bar)
        } yield o.asInstanceOf[Funky[A, B]]
      } else None
    }

    def describe: String = s"Funky[${typA.describe}, ${typB.describe}]"
  } 
```

Enter fullscreen mode Exit fullscreen mode

类型类实例用`Funky`类的类型参数的`Typeable`实例来参数化。这允许我们对所有类型参数也是`Typeable`的`Funky`类型使用实例。

在实例参数的帮助下，我们可以创建一个 cast 方法，试图将给定值转换为一个`Funky[A, B]`，同时它也可以转换`Funky`中的值。

正如我们从示例中看到的，即使有两个类型参数和两个字段，转换过程也已经很复杂了。添加更多的字段和类型参数需要更多的转换步骤。此外，强制转换不是由编译器执行的(例如，您可能很容易错过字段的强制转换步骤)，这意味着它面临强制转换失败的风险。

## 类型标签

另一种类型安全转换的方法是使用 Scala 的[类型标签](http://docs.scala-lang.org/overviews/reflection/typetags-manifests.html)。类型标签是 Scala 类型的完整类型描述，作为编译时生成的运行时值。与 Shapeless 类似，type 标签也是通过 type 类实现的。type 类提供了在运行时访问泛型类型参数的类型信息的能力。

与 Shapeless 不同，这种转换不是基于检查类内部的元素。相反，它是基于比较类型标签。类型标记可以告诉我们一个类型标记的类型是否符合另一个类型标记的类型。类型标签允许检查类型是否相等或者是否有子类型关系。

```
import scala.reflect.runtime.universe._

def handle[A: TypeTag](a: A): Unit =
  typeOf[A] match {
    case t if t =:= typeOf[List[String]] =>
      // list is a string list
      val r = a.asInstanceOf[List[String]].map(_.length).sum
      println("strings: " + r)

    case t if t =:= typeOf[List[Int]] =>
      // list is an int list
      val r = a.asInstanceOf[List[Int]].sum
      println("ints: " + r)

    case _ => // ignore rest
  }

val ints: List[Int] = Nil

handle(List("hello", "world")) // output: "strings: 10"
handle(List(1, 2, 3))          // output: "ints: 6"
handle(ints)                   // output: "ints: 0" it works! 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们在类型标签的帮助下实现了以前例子中常见的`handle`函数。我们声明输入有一个类型标签，然后将该类型与一些现有的已知类型进行比较:字符串列表和整数列表。如果类型之间匹配，我们可以使用`asInstanceOf`安全地执行强制转换。因为 type 参数是按类型匹配的，所以空的整数列表将被识别为整数列表，而不是字符串列表。

### 类型标签和未知类型

上一个示例中显示的方法的缺点是，我们必须提供一个类型标记来执行类型匹配。在这个例子中，我们依靠提供给函数的类型标签实例来恢复泛型类型的类型信息。在许多情况下，函数签名可能仅限于一个没有任何类型标签信息的`Any => Unit`。解决这个问题的一个方法是提供类型标签信息作为类型的一部分。

```
import scala.reflect.runtime.universe._

class Funky[A, B](val foo: A, val bar: B) {
  override def toString: String = s"Funky($foo, $bar)"
}

final case class FunkyCollection[A: TypeTag, B: TypeTag](funkySeq: Seq[Funky[A, B]]) { // Why final? We'll get back to this.
  val selfTypeTag = typeTag[FunkyCollection[A, B]]

  def hasType[Other: TypeTag]: Boolean =
    typeOf[Other] =:= selfTypeTag.tpe

  def cast[Other: TypeTag]: Option[Other] =
    if (hasType[Other])
      Some(this.asInstanceOf[Other])
    else
      None
}

val a: FunkyCollection[String, Int] = FunkyCollection(Seq(new Funky("foo", 2)))
val b: FunkyCollection[_, _] = a

b.hasType[FunkyCollection[String, Int]] // true
b.hasType[FunkyCollection[Int, String]] // false
b.cast[FunkyCollection[String, Int]]    // Some(a)
b.cast[FunkyCollection[Int, String]]    // None 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们为上一个例子中的`Funky`对象集合创建了一个包装器。除了一系列的`Funky`对象之外，`FunkyCollection`的构造需要类型参数的类型标签作为构造的一部分。然后使用类型标签来具体化`FunkyCollection`本身的类型标签，该标签可用于与其他类型进行比较。

`cast`方法用于根据给定类型和存储在对象中的类型标签的关系执行类型安全转换。如果类型匹配，我们可以使用`asInstanceOf`转换对象。

不幸的是，有可能在对象中意外获得错误的类型标记。如果另一个类扩展了`FunkyCollection`，类型标签将在那个类中保持不变。因此，所有的`hasType`和`cast`比较都将针对`FunkyCollection`进行，而不是扩展`FunkyCollection`的类型。通过覆盖`selfTypeTag`来使用扩展类的类型标签可以防止这种情况。然而，在这样做的时候，一个隐藏的需求被提供给了扩展`FunkyCollection`的类，因此增加了引入新错误的可能性。因此，您可能想要使用`final`关键字将`FunkyCollection`从扩展中密封出来以防止这个问题。

我们也可以基于`cast`方法创建一个提取器。

```
import scala.reflect.runtime.universe._

object FunkyCollection {
  def extractor[A: TypeTag, B: TypeTag] = new FunkyExtractor[A, B]
}

class FunkyExtractor[A: TypeTag, B: TypeTag] {
  def unapply(a: Any): Option[FunkyCollection[A, B]] = a match {
    case kvs: FunkyCollection[_, _] => kvs.cast[FunkyCollection[A, B]]
    case _ => None
  }
}

val stringIntExt = FunkyCollection.extractor[String, Int]
val a: FunkyCollection[String, Int] = FunkyCollection(Seq(new Funky("foo", 2)))
val b: FunkyCollection[_, _] = a

b match {
  case stringIntExt(collection) =>
    // `collection` has type `FunkyCollection[String, Int]`
    ...

  case _ =>
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们有一个特殊的类`FunkyExtractor`，它提供了提取`FunkyCollection`值的`unapply`方法。该类用类型标签参数化，与`FunkyCollection`类型结合使用，对`FunkyCollection`值执行转换操作。

### 提取样板文件

嵌入类型标记和创建强制转换方法的模式在所有类型中几乎是相同的。让我们将这些特征提取为一个特征:

```
trait TypeTaggedTrait[Self] { self: Self =>
  val selfTypeTag: TypeTag[Self]

  def hasType[Other: TypeTag]: Boolean =
    typeOf[Other] =:= selfTypeTag.tpe

  def cast[Other: TypeTag]: Option[Other] =
    if (hasType[Other])
      Some(this.asInstanceOf[Other])
    else
      None
}

abstract class TypeTagged[Self: TypeTag] extends TypeTaggedTrait[Self] { self: Self =>
  val selfTypeTag: TypeTag[Self] = typeTag[Self]
} 
```

Enter fullscreen mode Exit fullscreen mode

特征`TypeTaggedTrait`为任何扩展它的类型提供了`hasType`和`cast`方法。这些方法使用抽象字段`selfTypeTag`来帮助比较类型和对象自身的类型。特征的类型参数`Self`用于表示扩展特征的类型。为了防止使用 trait 不能转换成的类型作为类型参数，trait 需要 trait 实现来扩展类型参数。这是通过添加类型参数作为[自身类型注释](http://daily-scala.blogspot.co.uk/2010/02/self-annotation-vs-inheritance.html)来完成的。

特征的`selfTypeTag`可以使用抽象类`TypeTagged`隐式提供。通过扩展`TypeTagged`类，类可以通过类型参数自动提供正确的类型标签。

既然我们已经将`cast`方法提取到它自己的特征中，我们可以围绕该特征创建一个提取器类:

```
class TypeTaggedExtractor[T: TypeTag] {
  def unapply(a: Any): Option[T] = a match {
    case t: TypeTaggedTrait[_] => t.cast[T]
    case _ => None
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

与上一节中的`FunkyExtractor`一样，`TypeTaggedExtractor`为给定的类型创建一个提取器的实例。提取器在`TypeTaggedTrait`上进行部分模式匹配，并尝试使用对象的`cast`方法转换为给定的类型。

使用特征和提取器，我们可以重构`FunkyCollection`来使用这些一般化的特性:

```
object FunkyCollection {
  def extractor[A: TypeTag, B: TypeTag] = new TypeTaggedExtractor[FunkyCollection[A, B]]
}

final case class FunkyCollection[A: TypeTag, B: TypeTag](funkySeq: Seq[Funky[A, B]])
  extends TypeTagged[FunkyCollection[A, B]] 
```

Enter fullscreen mode Exit fullscreen mode

正如前面的`FunkyCollection`示例中提到的，您可能想要密封扩展了`TypeTagged`或`TypeTaggedTrait`的类，以防止不正确的类型标签显示为`selfTypeTag`。

### 铸造时间与输入尺寸的关系

由于转换是基于标记而不是值，所以随着输入大小的增加，转换所花费的时间应该大致保持不变。

```
def toFunkyCollection(i: Seq[Int]) = FunkyCollection[String, Int] {
  i.map(v => new Funky(v.toString, v))
}

val coll1: Any = toFunkyCollection((1 to 100).toList)
val coll2: Any = toFunkyCollection((1 to 1000).toList)
val coll3: Any = toFunkyCollection((1 to 10000).toList)
val coll4: Any = toFunkyCollection((1 to 100000).toList)
val coll5: Any = toFunkyCollection((1 to 1000000).toList)
val coll6: Any = toFunkyCollection((1 to 10000000).toList)

time { extractor1.unapply(coll1) } // 1 ms
time { extractor1.unapply(coll2) } // 0 ms
time { extractor1.unapply(coll3) } // 0 ms
time { extractor1.unapply(coll4) } // 0 ms
time { extractor1.unapply(coll5) } // 0 ms
time { extractor1.unapply(coll6) } // 0 ms 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们执行与前面类似的分析。铸造的效果在毫秒尺度上几乎看不到。

然而，指出类型标签在多线程环境中可能有线程安全或性能问题是公平的，这取决于您使用的 Scala 版本。在 Scala 2.10 中，类型标签不是[线程安全的](http://docs.scala-lang.org/overviews/reflection/thread-safety.html)。Scala 2.11 通过在反射 API 的关键位置引入锁定，修复了线程安全问题。因为类型标记在内部使用同步，所以使用类型标记进行转换的性能可能比在并发转换值时使用 Typeable 要差得多。

## 结论

在本文中，我演示了几种在 Scala 中进行模式匹配时避免类型擦除的方法。我展示了两个如何通过重构代码来解决整个问题的例子。我还展示了如何使用 Shapeless 的 Typeable 和 Scala 的 type 标签进行类型安全转换。

[重构代码](#avoid-losing-the-generic-type)不依赖模式匹配提供了最干净的解决方案，但这并不总是可行的。一些库，比如 Akka，提供了 API 来强制其用户在`Any`类型上进行模式匹配。当与库交互时，这个问题通常可以通过用没有类型参数的类型包装有类型参数的类型来避免。

解决泛型类型模式匹配的另一种方法是使用 Shapeless 的 Typeable 或 Scala 的 type 标签。Typeable 和 TypeCase 一起提供了一个易于使用的 API 来执行类型安全转换。它的转换机制是基于运行时的类型检查值，因此它不遵循转换的所有编译时语义。[类型标签](#type-tags)提供了一个 API，用于在运行时对提升为值的类型执行类型检查。类型标签不像 Typeable 那样直接使用，但是它的类型检查过程更加严格。Type 标签也需要线程同步，而 Typeable 不需要。

我要感谢 Miles Sabin】指出了使用类型标签的问题以及参与审阅本文的所有人。我已经将代码示例上传到 [Github Gist](https://gist.github.com/jkpl/5279ee05cca8cc1ec452fc26ace5b68b) 供大家试用。感谢阅读！