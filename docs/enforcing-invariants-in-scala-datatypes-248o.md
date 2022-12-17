# 在 Scala 数据类型中实施不变量

> 原文：<https://dev.to/lepovirta/enforcing-invariants-in-scala-datatypes-248o>

Scala 提供了许多工具来帮助我们构建运行时错误更少的程序。不要依赖空值，推荐的做法是使用`Option`类型。不抛出异常，`Try`和`Either`类型被用来表示潜在的错误场景。这些特性的共同点是，它们用于捕获类型系统中的运行时特性，从而将运行时场景处理提升到编译阶段:在代码中显式处理空值、异常和其他运行时特性之前，程序不会编译。

在他的“战略性 Scala 风格”博客系列中，李探索了使用自检和结构强制等技术在数据类型中强制不变量的[模式。博文中描述的一些技术依赖于运行时断言。因此，虽然它们可以防止无效数据出现在数据类型中，但数据类型结构并没有完全体现在类型系统中。](http://www.lihaoyi.com/post/StrategicScalaStyleDesigningDatatypes.html)

在这篇博文中，我将通过演示使用类型安全的编译时技术来增强不变量的方法来扩展这些技术。首先，我将使用一个示例数据类型演示运行时断言风格是如何工作的。然后，我将展示如何以类型安全的方式在示例数据类型中实施不变量。最后，我将简要介绍将 case 类用于具有类型安全不变验证的数据类型的缺陷，以及我们如何使用类继承系统和 ScalaMeta 宏中的技巧重新获得 case 类的特性。

## 使用运行时断言进行不变验证

例如，让我们为会议创建一个数据类型。会议有名称、开始日期和结束日期。会议名称不能为空，结束日期不能早于开始日期。使用运行时断言技术，数据类型可以表示如下:

```
import java.time.LocalDate

case class Conference(name: String, startDate: LocalDate, endDate: LocalDate) {
  require(name.nonEmpty)
  require(startDate.isEqual(endDate) || startDate.isBefore(endDate))
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们使用 Scala 的内置函数`require`来确保我们的数据类型符合我们的需求。为了确保我们已经捕获了潜在的运行时错误，我们可以用 try-catch 块或者`Try`构造函数:
来包装数据类型构造

```
try {
  val invalidConference = new Conference("", LocalDate.now(), LocalDate.now())
} catch {
  case ex: IllegalArgumentException =>
    // Handle the error in any way you like here
    println("Invalid parameters when creating a conference: " + ex.getMessage)
}

val invalidConference: Try[Conference] = Try(new Conference("", LocalDate.now(), LocalDate.now())) 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题与任何其他抛出错误的函数一样:函数是部分的，而不是全部的。这意味着只有输入的子集将产生对应于预期类型的值，而其余的输入将导致程序执行脱离正常的程序流。无效输入的结果不在类型系统中表示，因此它不是类型安全的。

实际上，非类型安全的不变验证的问题是，它将捕获错误的负担放在了用户一方。未能捕捉到错误会在您通常期望它们正常工作的地方产生无法工作的软件。随着构造数据类型的地方越来越多，捕捉错误失败的机会也越来越大。

## 以类型安全的方式实施不变量

为了减轻手动捕捉错误的负担，我们可以通过在类型系统中表示潜在的运行时错误来使不变验证类型安全。这可以通过向用户提供一个构造函数来完成，该构造函数返回构造值(会议的一个实例)或错误值。

```
class Conference private (
  name: String,
  startDate: LocalDate,
  endDate: LocalDate
)

object Conference {
  def apply(name: String, startDate: LocalDate, endDate: LocalDate): Option[Conference] =
    if (name.nonEmpty && (startDate.isEqual(endDate) || startDate.isBefore(endDate))) {
      Some(new Conference(name, startDate, endDate))
    } else {
      None
    }
}

val validConference: Option[Conference] = Conference("ScalaDays 2017", LocalDate.of(2017, 5, 30), LocalDate.of(2017, 6, 2))
val invalidConference: Option[Conference] = Conference("", LocalDate.now(), LocaDate.now()) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们已经为 conference 数据类型定义了一个类，并在该数据类型的同伴对象中定义了一个替代的构造函数来验证输入。为了简单起见，我们将备选构造函数的返回类型表示为`Option[Conference]`，其中缺少值表示验证错误。

我们还隐藏了数据类型的主构造函数。这样，构造函数只能在替代构造函数的范围内使用，以创建经过验证的实例。如果构造函数没有隐藏，用户可能会意外地使用错误的构造函数来创建无效的实例。隐藏主构造函数也有效地防止了类的扩展。

构造的更详细的表示可以包括关于不变验证错误的更多细节。当使用`Either`类型时，类型中的一边可用于表示一个或多个验证错误，而另一边将表示成功值。例如在`Either[List[Error], Conference]`中，左侧表示构建过程中发现的所有错误，右侧表示成功构建的会议实例。无论使用哪种返回类型，构建类型安全值的模式都是相似的:返回值必须被“解包”才能达到实际值。

我们可以通过为数据类型的字段创建有意义的数据类型来进一步增强验证过程。例如，会议的名称可以使用自己的验证规则:

```
class Name private (val name: String) extends AnyVal

object Name {
  def apply(name: String): Option[Name] =
    if (name.nonEmpty) Some(new Name(name))
    else None
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，名称被封装在一个值类中。通过使数据类型成为一个值类(也就是说，它扩展了`AnyVal`)，我们可以在某些情况下避免在字符串周围分配对象的开销，但同时享受与普通字符串相比它所提供的类型安全。作为一个值类，该类也将根据它所包装的类型自动拥有`equals`和`hashCode`方法。

有了`Name`类型，我们可以让`Conference`类型使用它，而不必重复验证过程。

```
class Conference private (
  name: Name,
  startDate: LocalDate,
  endDate: LocalDate
)

object Conference {
  def apply(name: Name, startDate: LocalDate, endDate: LocalDate): Option[Conference] =
    if (startDate.isEqual(endDate) || startDate.isBefore(endDate)) {
      Some(new Conference(name, startDate, endDate))
    } else {
      None
    }
}

val validConference: Option[Conference] = for {
  name <- Name("ScalaDays 2017")
  conference <- Conference(name, LocalDate.of(2017, 5, 30), LocalDate.of(2017, 6, 2))
} yield conference 
```

Enter fullscreen mode Exit fullscreen mode

## 在 case 类数据类型中逃避不变验证

在 Scala 中，case 类给常规的老类带来了一大堆好东西。例如，case 类有一个自动实现的`equals`、`hashCode`、`toString`方法，以及一个基于类字段的模式匹配提取器。

Case 类对于实现数据类型来说很方便，所以使用它们作为数据类型的基本类型是有意义的，在这种情况下，不变量以类型安全的方式进行验证。然而，它们也使得绕过验证检查变得容易。与常规类不同，case 类有不止一个需要隐藏的构造函数。除了常规的构造函数，case 类也可以使用自动生成的`apply`函数和`copy`方法来构造。

```
case class Conference private (
  name: String,
  startDate: LocalDate,
  endDate: LocalDate
)

object Conference {
  def create(name: String, startDate: LocalDate, endDate: LocalDate): Option[Conference] =
    if (name.nonEmpty && (startDate.isEqual(endDate) || startDate.isBefore(endDate))) {
      Some(new Conference(name, startDate, endDate))
    } else {
      None
    }
}

// Invalid instance through apply function
val invalidConference1: Conference = Conference("", LocalDate.now(), LocalDate.now())

// Valid instance...
val Some(validConference) = Conference.create("ScalaDays 2017", LocalDate.of(2017, 5, 30), LocalDate.of(2017, 6, 2))

// ...can be a gateway to an invalid instance.
val invalidConference2: Conference = validConference.copy(name = "") 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们用 case 类替换了普通的`Conference`类。我们仍然可以使用 companion 对象中的`create`函数来创建数据类型的验证实例。然而，我们仍然可以访问自动生成的`apply`方法，并以这种方式创建无效的实例。此外，我们可以使用`copy`方法从有效的数据类型创建无效的数据类型副本。

通过用一个不会产生无效副本的方法手动覆盖它，可以删除`copy`方法，但是`apply`函数既不能被覆盖也不能被隐藏。无法替换函数是验证构造函数必须使用不同名称的原因。这些特性使得 case 类对于以类型安全的方式实施不变量的数据类型来说不可行。

## 恢复案例类特征

如前所述，case 类包含许多有用的特性。因此，仅仅因为它们的`apply`和`copy`构造函数与不变量验证过程冲突而丢失它们是很可惜的。我们如何保持 case 类的特性不与验证冲突呢？

让 case 类没有`apply`和`copy`构造函数的一种方法是让 case 类[抽象并密封](https://gist.github.com/tpolecat/a5cb0dc9adeacc93f846835ed21c92d2)。像任何其他类一样，case 类可以被抽象。这意味着该类可以定义为在其子类中实现而开放的函数接口。这也意味着类必须被扩展，因为不能直接创建它的实例，即使子类已经没有什么可实现的了。因此，不会为 case 类生成`apply`或`copy`构造函数，因为没有已知的构造函数来实现它们。

因为没有`final`修饰符来限制 traits 和`abstract`类的子类化，我们将不得不使用`sealed`修饰符来代替。`sealed`修饰符允许我们限制对源文件创建子类的范围。因此，如果不访问我们提供的构造函数，就不能创建任何实例。

```
sealed abstract case class Conference(name: String, startDate: LocalDate, endDate: LocalDate)

object Conference {
  def apply(name: Name, startDate: LocalDate, endDate: LocalDate): Option[Conference] =
    if (name.nonEmpty && (startDate.isEqual(endDate) || startDate.isBefore(endDate)))
      Some(new Conference(name, startDate, endDate) {})
    else
      None
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们已经重新创建了早期的`Conference`数据类型，但是这次是作为一个密封的抽象 case 类。我们构造实例的唯一方法是通过我们提供的自定义构造函数。然而，由于数据类型现在是一个 case 类，我们可以使用它的 case 类特性，比如自动生成的`equals`和`hashCode`函数。

在常规类中获取 case 类特性的另一种方法是使用 [ScalaMeta](http://scalameta.org/) 的`@data`注释宏。`@data`注释宏自动为任何使用注释的常规类生成案例类特征。注记允许选择每个类包含哪些案例类要素。

为了使用注释，我们首先需要为项目启用 ScalaMeta 和 [paradise 编译器插件](https://github.com/scalameta/paradise)。这里有一个需要添加到我们的 SBT 配置中的例子:

```
// Resolvers for the ScalaMeta library and paradise plugin
resolvers in ThisBuild += Resolver.url("scalameta", url("http://dl.bintray.com/scalameta/maven"))(Resolver.ivyStylePatterns)

// Enable paradise compiler plugin
addCompilerPlugin("org.scalameta" % "paradise" % "3.0.0.138" cross CrossVersion.full)
scalacOptions += "-Xplugin-require:macroparadise"

// Add library dependency for ScalaMeta
libraryDependencies += "org.scalameta" %% "scalameta" % "1.3.0" 
```

Enter fullscreen mode Exit fullscreen mode

启用库和编译器插件后，我们可以使用`@data`注释。

```
@data(copy = false, apply = false)
class Conference private (name: String, startDate: LocalDate, endDate: LocalDate)

object Conference {
  def apply(name: String, startDate: LocalDate, endDate: LocalDate): Option[Conference] =
    if (name.nonEmpty && (startDate.isEqual(endDate) || startDate.isBefore(endDate)))
      Some(new Conference(name, startDate, endDate))
    else
      None
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们已经用`@data`注释对`Conference`类进行了注释。默认情况下，为该类启用所有案例类功能。这里我们禁用了`copy`和`apply`构造函数，因此我们可以完全控制数据类型用户可见的构造函数。

## 结论

在本文中，我展示了一种在 Scala 数据类型中强制使用不变量的类型安全方法。我已经展示了非类型安全的不变验证的常用技术，以及它们通常遇到的问题。我还展示了将 case 类与类型安全不变验证结合使用的缺陷，以及如何使用类继承和 ScalaMeta 宏来避免这些问题。

像往常一样，我在 Github Gist 中给出了不同的不变量验证技术的例子。感谢阅读！