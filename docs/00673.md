# 科特林 DSL:从理论到实践

> 原文：<https://dev.to/ivanosipov/kotlin-dsl-from-theory-to-practice-6h7>

SQL、RegExp、Gradle——它们有什么共同点？所有这些都代表了使用特定领域语言(DSL)的一个例子。这种类型的语言旨在解决特定的问题，例如数据库查询、在文本中查找匹配或构建过程描述。Kotlin 为构建您自己的特定领域语言提供了大量特性。在本文中，我们将发现开发人员的工具包，并为现实世界的域实现 DSL。

我将尽可能简单地解释这种语言的语法，但是，这篇文章仍然吸引那些将 Kotlin 视为定制 DSL 构建语言的开发人员。在文章的最后，我将提到 Kotlin 值得考虑的缺点。所展示的代码片段与 Kotlin 版本 1.2.0 相关，可以在 GitHub 上获得。

# 什么是 DSL？

所有的编程语言都可以分为通用语言和特定领域语言。SQL、正则表达式和 build.gradle 经常被引用作为 DSL 的例子。这些语言在功能上是有限的，但是它们能够有效地解决某个问题。它们允许你编写命令式代码(我们不应该解释如何解决问题)，但或多或少是以声明的方式(我们只是声明任务)，以便获得基于给定数据的解决方案。

假设您有一个标准的过程定义，它最终可以被更改和增强，但是一般来说，您希望将它与不同的数据和结果格式一起使用。通过创建 DSL，您可以创建一个灵活的工具来解决一个主题领域内的各种问题，而不管解决方案是如何获得的。因此，您创建了一种 API，如果掌握了这种 API，就可以简化您的工作，并使系统长期保持最新变得更加容易。

本文讨论在 Kotlin 中构建一个“嵌入式”DSL，作为一种基于通用语言语法实现的语言。你可以在这里阅读更多相关信息[。](https://en.wikipedia.org/wiki/Domain-specific_language#Usage_patterns)

# 实现区域

在我看来，使用和演示 Kotlin DSL 的最佳方式之一就是测试。

假设您来自 Java 世界。您多久会面临一次声明扩展数据模型的实体实例的情况？您可能已经使用了一些构建器，或者更糟糕的是，使用特殊的实用程序类来填充默认的值。你有过多少被覆盖的方法？您多长时间需要对默认值进行一次小小的更改，现在需要付出多大的努力？

如果这些问题只会激起消极的情绪，那么这篇文章是给你的。

这就是我们在教育领域的项目中已经做了很长时间的方法:我们使用构建器和实用程序类来测试我们最重要的模块之一(学校课程表)。现在这种方法已经让位于 Kotlin 语言和 DSL，后者用于描述测试场景和检查结果。在整篇文章中，您可以看到我们是如何利用 Kotlin 的，因此测试调度子系统不再是一种折磨。

在本文中，我们将深入探讨构建 DSL 的细节，该 DSL 有助于测试构建教师和学生时间表的算法。

# 按键工具

下面是一些基本的语言特性，允许你用 Kotlin 编写更简洁的代码，并创建你自己的 DSL。下表展示了值得使用的主要语法增强。仔细看一下。如果您不熟悉这些工具，您可能想阅读整篇文章。如果你不知道其中的一两个，请随意快进到相应的部分。如果对你来说没有什么新鲜的，直接跳到文章末尾的 DSL 缺点回顾。欢迎在评论中提出更多的工具。

| 工具 | DSL 语法 | 通用语法 |
| --- | --- | --- |
| 运算符重载 | `collection += element` | `collection.add(element)` |
| 键入别名 | `typealias Point = Pair<Int, Int>` | 创建空的继承类和其他管道胶带 |
| 获取/设置方法约定 | `map["key"] = "value"` | `map.put("key", "value")` |
| 解构声明 | `val (x, y) = Point(0, 0)` | `val p = Point(0, 0); val x = p.first; val y = p.second` |
| 括号外的λ | `list.forEach { ... }` | `list.forEach({...})` |
| 扩展功能 | `mylist.first(); // there isn’t first() method in mylist collection` | 效用函数 |
| 中缀函数 | `1 to "one"` | `1.to("one")` |
| 带接收器的λ | `Person().apply { name = «John» }` | 不适用的 |
| 上下文控制 | `@DslMarker` | 不适用的 |

有新发现吗？如果是，那我们继续。

我故意省略了委托属性，因为在我看来，它们对于构建 DSL 毫无用处，至少在我们的例子中是这样。使用上面的特性，我们可以编写更干净的代码，摆脱大量“嘈杂”的语法，使开发更加愉快(可能吗？).

我喜欢我在《科特林在行动》一书中遇到的比较:在自然语言中，像英语一样，句子是由单词组成的，语法规则定义了这些单词的组合方式。

类似地，在 DSL 中，一个操作可以由几个方法调用来构造，类型检查保证了构造是有意义的。当然，调用的顺序并不总是显而易见的，但这完全取决于 DSL 设计者。

需要强调的是，本文研究的是一种“嵌入式 DSL”，因此它是基于一种通用语言，即 Kotlin。

## 最终结果示例

在我们开始设计我们自己的领域特定语言之前，我想向您展示一个例子，看看您在阅读本文后能够创建什么。完整的代码可以在 GitHub 仓库的这个[链接](https://github.com/ivan-osipov/kotlin-dsl-example)上找到。

以下基于 DSL 的代码旨在测试教师对学生的分配情况。在这个例子中，我们有一个固定的时间表，我们检查课程是否被安排在老师和学生的时间表中。

```
schedule {
    data {
        startFrom("08:00")
        subjects("Russian",
                "Literature",
                "Algebra",
                "Geometry")
        student {
            name = "Ivanov"
            subjectIndexes(0, 2)
        }
        student {
            name = "Petrov"
            subjectIndexes(1, 3)
        }
        teacher {
           subjectIndexes(0, 1)
           availability {
             monday("08:00")
             wednesday("09:00", "16:00")
           } 
        }
        teacher {
            subjectIndexes(2, 3)
            availability {
                thursday("08:00") + sameDay("11:00") + sameDay("14:00")
            }
        }
        // data { } won't be compiled here because there is scope control with
        // @DataContextMarker
    } assertions {
        for ((day, lesson, student, teacher) in scheduledEvents) {
            val teacherSchedule: Schedule = teacher.schedule
            teacherSchedule[day, lesson] shouldNotEqual null
            teacherSchedule[day, lesson]!!.student shouldEqual student
            val studentSchedule = student.schedule
            studentSchedule[day, lesson] shouldNotEqual null
            studentSchedule[day, lesson]!!.teacher shouldEqual teacher
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 工具箱

上面已经列出了构建 DSL 的所有特性。上一节的示例中使用了它们中的每一个。您可以在我在 GitHub 上的[项目](https://github.com/ivan-osipov/kotlin-dsl-example)中研究如何定义这样的 DSL 结构。

为了演示不同工具的用法，我们将在下面再次引用这个例子。请记住，所描述的方法仅用于说明目的，并且可能有其他选项来实现期望的结果。

那么，就让我们一个一个去发现这些工具吧。一些语言特性在与其他特性结合起来时是最强大的，这个列表中的第一个是括号外的 lambda。

### λ出括号

[文档](https://kotlinlang.org/docs/reference/lambdas.html#higher-order-functions)

lambda 表达式是一个代码块，可以传递给函数，保存或调用。在 Kotlin 中，lambda 类型是以如下方式定义的:(参数类型列表)->返回类型。遵循这个规则，最原始的 lambda 类型是()-> Unit，其中 Unit 相当于 Void，但有一个重要的例外。在 lambda 的末尾，我们不必写*返回...*建筑。因此，我们总是有一个返回类型，但在 Kotlin 中，这是隐式完成的。

以下是将 lambda 赋给变量的基本示例:

`val helloPrint: (String) -> Unit = { println(it) }`

通常，编译器试图从已知的类型中推断出类型。在我们的例子中，有一个参数。这个 lambda 可以按如下方式调用:

`helloPrint("Hello")`

在上面的例子中，lambda 有一个参数。在 lambda 中，默认情况下调用这个参数，但是如果有更多的参数，您必须显式地指定它们的名称，或者使用下划线来忽略它们。见下面这样一个案例:

```
val helloPrint: (String, Int) -> Unit = { _, _ -> println("Do nothing") } 
helloPrint("Does not matter", 42) //output: Do nothing 
```

Enter fullscreen mode Exit fullscreen mode

基本工具——您可能已经从 Groovy 中知道了——是括号外的 lambda。再看看文章开头的例子:除了标准结构，几乎所有花括号的使用都是λ。至少有两种制作 x {...}:-like 结构:

*   对象 x 和它的一元运算符*调用*(我们后面会讨论)；
*   取λ的函数 x。

在这两种情况下，我们都使用 lambdas。假设有一个函数 x()。在 Kotlin 中，如果 lambda 是函数的最后一个参数，它可以放在括号之外。此外，如果 lambda 是函数的唯一参数，括号可以省略。因此，构造 x({...})可以转化为 x() {}，然后，通过省略括号，我们得到 x {}。我们是这样声明这些函数的:

`fun x( lambda: () -> Unit ) { lambda() }`

简单来说，单行函数也可以写成这样:

`fun x( lambda: () -> Unit ) = lambda()`

但是如果 x 是类实例或者对象而不是函数呢？下面是另一个有趣的解决方案，它基于一个基本的特定领域概念:操作符重载。

### 运算符重载

[文档](https://kotlinlang.org/docs/reference/operator-overloading.html)

Kotlin 提供了广泛但有些有限的操作符。*操作符*修饰符能够通过约定来定义函数，这些约定将在特定条件下被调用。一个明显的例子是，如果在两个对象之间使用“+”运算符，就会执行加号函数。操作员的完整列表可以通过上面的链接在文档中找到。

让我们考虑一个更简单的操作符:*调用*。本文的主要示例从定义负责测试时间表的代码块的*时间表{ }* 结构开始。这个构造的构建方式与上面提到的略有不同:我们使用 invoke 操作符+“括号外的 lambda”。

定义了调用操作符后，我们现在可以使用*调度(...)*构造，虽然*日程*是宾语。事实上，当你调用*的时间表(...)*，编译器将其解释为 *schedule.invoke(...)*。让我们看看*调度*是如何声明的:

```
object schedule {
    operator fun invoke(init: SchedulingContext.() -> Unit)  { 
        SchedulingContext().init()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*schedule* 标识符指的是唯一一个由特殊关键字 *object* 标记的 *schedule* 类实例(singleton)(你可以在这里找到关于这些对象[的更多信息)。因此，我们调用 *schedule* 实例的 *invoke* 方法，接收一个 lambda 作为单个参数，并将其放在括号之外。因此，*日程安排{...}* 施工配合如下:](https://kotlinlang.org/docs/reference/object-declarations.html#object-declarations)

`schedule.invoke( { code inside lambda } )`

然而，如果你仔细观察 invoke 方法，你会看到的不是一个普通的 lambda，而是一个“带有接收者的 lambda”或“带有上下文的 lambda”，其类型被定义为:

`SchedulingContext.() -> Unit`

我们来详细考察一下。

### 带接收器的λ

[文档](https://kotlinlang.org/docs/reference/lambdas.html#function-literals-with-receiver)

Kotlin 使我们能够为 lambda 表达式设置一个上下文(上下文和接收者在这里的意思是一样的)。上下文只是一个对象。上下文类型与 lambda 表达式类型一起定义。这种 lambda 获取上下文类中非静态方法的属性，但它只能访问该类的公共方法。

普通 lambda 的类型定义为()-> Unit，而带有 X 上下文的 lambda 的类型定义如下:X.()-> Unit。并且，如果正常的话，lambdas 可以按通常的方式调用:

```
val x : () -> Unit = {}
x() 
```

Enter fullscreen mode Exit fullscreen mode

同时，带有上下文的 lambda 需要一个上下文:

```
class MyContext

val x : MyContext.() -> Unit = {}

//x() //won’t be compiled, because a context isn’t defined 

val c = MyContext() //create the context

c.x() //works

x(c) //works as well 
```

Enter fullscreen mode Exit fullscreen mode

我想提醒您，我们已经在 schedule 对象中定义了 invoke 操作符(参见上一段),它允许我们使用以下构造:

`schedule { }`

我们使用的 lambda 具有 SchedulingContext 类型的上下文。这个类中有一个数据方法。结果，我们得到了下面的构造:

```
schedule { 
    data { 
        //... 
    } 
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您可能已经猜到的那样，data 方法也接受一个带有上下文的 lambda。然而，这是一个不同的背景。因此，我们得到了同时包含几个上下文的嵌套结构。为了了解它是如何工作的，让我们去掉例子中所有的语法糖:

```
schedule.invoke({ 
    this.data({ }) 
}) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这非常简单。让我们看看调用操作符的实现。

```
operator fun invoke(init: SchedulingContext.() -> Unit)  { 
    SchedulingContext().init()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们调用上下文 SchedulingContext()的构造函数，然后使用创建的对象( *context* )调用带有作为参数传递的 *init* 标识符的 lambda。这有点类似于一般的函数调用。

因此，在一行中— SchedulingContext()。init() —我们创建上下文并调用传递给操作符的 lambda。更多的例子，请考虑 Kotlin 标准库中的 *apply* 和 *with* 方法。

在上一个例子中，我们发现了 *invoke* 操作符及其与其他工具的组合。接下来，我们将关注正式作为操作符并使代码更干净的工具——*get/set 方法约定*。

### 获取/设置方法约定

[文档](https://kotlinlang.org/docs/reference/operator-overloading.html#indexed)

当创建 DSL 时，我们可以实现一种通过一个或多个键来访问地图的方法。我们来看下面的例子:

```
availabilityTable[DayOfWeek.MONDAY, 0] = true 
println(availabilityTable[DayOfWeek.MONDAY, 0]) //output: true 
```

Enter fullscreen mode Exit fullscreen mode

为了使用方括号，我们需要用一个*操作符*修饰符来实现 *get* 或 *set* 方法(取决于我们需要什么——读取或更新)。你可以在 [GitHub](https://github.com/ivan-osipov/kotlin-dsl-example) 上的 *Matrix* 类中找到这样一个实现的例子。它是矩阵运算的简单包装器。下面，你可以看到关于这个主题的一段代码:

```
class Matrix(...) {
    private val content: List>
    operator fun get(i: Int, j: Int) = content[i][j]
    operator fun set(i: Int, j: Int, value: T) { content[i][j] = value }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以使用任何*得到*和*设置*的参数类型，唯一的限制是你的想象力。您可以自由地为 get/set 函数使用一个或多个参数，以便为数据访问提供方便的语法。Kotlin 的操作员提供了许多有趣的特性，这些特性在[文档](https://kotlinlang.org/docs/reference/operator-overloading.html)中有描述。

令人惊讶的是，在 Kotlin 标准库中有一个 *Pair* 类。很大一部分开发人员发现*对*是有害的:当你使用*对*时，链接两个对象的逻辑丢失了，因此它们为什么被配对是不透明的。接下来我将向您展示的两个工具将演示如何在不创建额外的类的情况下保持这一对有意义。

### 键入别名

[文档](https://kotlinlang.org/docs/reference/type-aliases.html)

假设我们需要一个带有整数坐标的地理点的包装类。实际上，我们可以使用 Pair 类，但是有了这样一个变量，我们就不知道为什么要将这些值配对了。

一个简单的解决方案是要么创建一个自定义类，要么更糟。Kotlin 通过以下符号的类型别名丰富了开发人员的工具包:

`typealias Point = Pair`

事实上，它只不过是重命名一个构造。由于这种方法，我们不再需要创建*点*类，因为它只会复制*对*。现在我们可以这样创建一个点:

`val point = Point(0, 0)`

然而，*对*类有两个属性，*第一个*和*第二个*，我们需要以某种方式重命名它们，以模糊所需的*点*和初始的*对*类之间的任何差异。当然，我们不能重命名属性本身(但是，您可以创建[扩展属性](https://kotlinlang.org/docs/reference/extensions.html#extension-properties)，但是在我们的工具包中有一个更值得注意的特性叫做*析构声明*。

### 析构声明

[文档](https://kotlinlang.org/docs/reference/multi-declarations.html#destructuring-declarations)

让我们考虑一个简单的情况:假设我们有一个 *Point* 类型的对象，正如我们已经知道的，只是一个重命名的类型对。如果我们看看标准库中的*对*类实现，我们会看到它有一个数据修饰符，指示编译器在这个类中实现 *componentN* 方法。下面我们来详细了解一下。

对于任何类，我们可以定义 *componentN* 操作符，它将负责提供对一个对象属性的访问。这意味着调用 *point.component1* 将等于调用 *point.first* 。为什么我们需要这样一个副本？

析构声明是一种将对象“分解”成变量的方法。这个功能允许我们编写以下类型的结构:

`val (x, y) = Point(0, 0)`

我们可以一次声明几个变量，但是会给它们赋什么值呢？这就是为什么我们需要生成的 *componentN* 方法:使用从 1 而不是 N 开始的索引，我们可以将一个对象分解成它的一组属性。所以，上面的结构等于下面的:

```
val pair = Point(0, 0)
val x = pair.component1()
val y = pair.component2() 
```

Enter fullscreen mode Exit fullscreen mode

反过来，它等于:

```
val pair = Point(0, 0)
val x = pair.first
val y = pair.second 
```

Enter fullscreen mode Exit fullscreen mode

其中*第一个*和*第二个*是点对象属性。

Kotlin 中的循环的*如下所示，其中 x 取值 1、2 和 3:*

`for(x in listOf(1, 2, 3)) { ... }`

注意主示例中 DSL 中的断言块。为方便起见，我重复一部分:

`for ((day, lesson, student, teacher) in scheduledEvents) { ... }`

这条线应该很明显。我们遍历一组 *scheduledEvents* ，其中的每个元素都被分解为四个属性。

### 扩展功能

[文档](https://kotlinlang.org/docs/reference/extensions.html#extensions)

向来自第三方库的对象或 Java 集合框架添加新方法是许多开发人员梦寐以求的。现在我们有这样的机会。这是我们声明扩展函数的方式:

`fun AvailabilityTable.monday(from: String, to: String? = null)`

与标准方法相比，我们添加类名作为前缀来定义我们扩展的类。在上面的例子中，AvailabilityTable 是 Matrix 类型的别名，由于 Kotlin 中的别名只不过是重命名，所以该声明等于下面的声明，这并不总是很方便:

`fun Matrix.monday(from: String, to: String? = null)`

不幸的是，除了不使用该工具或者只向特定的上下文类添加方法之外，我们无能为力。在这种情况下，魔法只出现在你需要的地方。此外，您甚至可以使用这样的函数来扩展接口。作为一个很好的例子，*第一个*方法扩展了任何*可迭代对象*:

`fun Iterable.first(): T`

本质上，任何基于 *Iterable* 接口的集合，不管元素类型如何，都会获得 *first* 方法。值得一提的是，我们可以在 context 类中放置一个扩展方法，从而只在这个上下文中访问扩展方法(类似于带有上下文的 lambda)。此外，我们可以为*可空*类型创建扩展函数(对*可空*类型的解释超出了这里的范围，但要了解更多细节，请参见[此链接](https://kotlinlang.org/docs/reference/null-safety.html#nullable-types-and-non-null-types))。例如，这就是我们如何使用标准 Kotlin 库中的函数 *isNullOrEmpty* 的方法，该函数扩展了 *CharSequence* 类型:

```
val s: String? = null
s.isNullOrEmpty() //true 
```

Enter fullscreen mode Exit fullscreen mode

下面是这个函数的签名:

`fun CharSequence?.isNullOrEmpty(): Boolean`

当在 Java 中使用这样的 Kotlin 扩展函数时，它们可以作为静态函数来访问。

### 中缀功能

[文档](https://kotlinlang.org/docs/reference/functions.html#infix-notation)

另一种美化语法的方法是使用中缀函数。简单来说，这个工具帮助我们在简单的情况下去掉过多的代码。主代码片段中的断言块演示了该工具的用例:

`teacherSchedule[day, lesson] shouldNotEqual null`

此构造等效于以下内容:

`teacherSchedule[day, lesson].shouldNotEqual(null)`

在某些情况下，括号和点可能是多余的。对于这种情况，我们可以对函数使用*中缀*修饰符。

在上面的代码中，构造 teacherSchedule[day，lesson]返回一个 Schedule 元素，函数 shouldNotEqual 检查该元素是否不为 null。

要声明中缀函数，您需要:

*   使用中缀修饰符。
*   仅使用一个参数。

结合最后两个工具，我们可以得到下面的代码:

`infix fun T.shouldNotEqual(expected: T)`

请注意，默认情况下，泛型类型是 Any 继承者(不可为 null)。但是，在这种情况下，我们不能使用 null —这就是为什么您应该显式定义 Any 类型。

### 上下文控制

[文档](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-dsl-marker/index.html)

当我们使用大量嵌套的上下文时，在较低的层次上，我们有可能得到一个混乱的组合。由于缺乏控制，以下无意义的建构成为可能:

```
schedule { //context SchedulingContext
    data { //context DataContext + external context SchedulingContext
        data { } //possible, as there is no context control
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Kotlin v.1.1 之前，就已经有了避免这种混乱的方法。它在于在嵌套上下文 DataContext 中创建自定义方法数据，然后用带有错误级别的不推荐使用的注释对其进行标记。

```
class DataContext {
    @Deprecated(level = DeprecationLevel.ERROR, message = "Incorrect context")
    fun data(init: DataContext.() -> Unit) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法消除了构建不正确 DSL 的可能性。然而，大量的安排上下文的方法会使我们做许多常规的工作，使人们不愿意进行任何上下文控制。

Kotlin 1.1 提供了一个新的控制工具——@ DSL marker 注释。它应用于您自己的注释，这些注释反过来用于标记您的上下文。让我们创建一个注释，并用工具箱中的新工具对其进行标记:

`@DslMarker annotation class MyCustomDslMarker`

现在我们需要标记上下文。在主示例中，它们是 SchedulingContext 和 DataContext。只要我们用公共的 DSL 标记来注释这两个类，就会发生以下情况:

```
@MyCustomDslMarker
class SchedulingContext { ... }

@MyCustomDslMarker
class DataContext { ... }

fun demo() {
    schedule { //context SchedulingContext
        data { //context DataContext + external context SchedulingContext is forbidden
            // data { } //will not compile, as contexts are annotated with the same DSL marker
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

尽管这种很酷的方法可以节省很多时间和精力，但仍然存在一个问题。看看主示例，或者更准确地说，看看这部分代码:

```
schedule {
    data {
        student {
            name = "Petrov"
        }
        ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在第三个嵌套层次上，我们得到了新的上下文，Student，它实际上是一个实体类。所以期望我们用@MyCustomDslMarker 注释部分数据模型，这在我看来是不正确的。在学生上下文中，data {}调用仍然被禁止，因为外部 DataContext 仍然在它的位置上，但是下面的结构仍然有效:

```
schedule {
    data {
        student {
            student { }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

试图用注释来解决问题会导致业务逻辑和测试代码的混淆，这当然不是最好的主意。这里有三种可能的解决方案:

1.  使用额外的上下文创建学生，例如 StudentContext。这闻起来很疯狂，超过了@DslMarker 的好处。
2.  为所有实体创建接口——例如 IStudent(无论名称如何),然后创建实现这些接口的存根上下文，最后，将实现委托给 Student 对象。这也近乎疯狂。`@MyCustomDslMarker` `class StudentContext(val owner: Student = Student()): IStudent by owner`
3.  使用 [@deprecated](https://dev.to/deprecated) 注释，如上面的例子所示。在这种情况下，这看起来是最好的解决方案:我们只是为所有可识别的对象添加了一个不推荐使用的扩展方法。`@Deprecated("Incorrect context", level = DeprecationLevel.ERROR)` `fun Identifiable.student(init: () -> Unit) {}`

总而言之，结合各种工具使您能够为您的现实世界目的构建一个非常方便的 DSL。

## DSL 使用的缺点

让我们更客观地看待在 Kotlin 中使用 DSL，并找出在您的项目中使用 DSL 的缺点。

### DSL 部件的复用

想象一下，你必须重用你的 DSL 的一部分。你想获取一部分代码，并使其易于复制。当然，在只有一个上下文的最简单的情况下，我们可以在扩展函数中隐藏 DSL 的可重复部分，但这在大多数情况下是行不通的。

也许你可以在评论中给我指出一些更好的选择，因为到目前为止，我只想到两个解决方案:添加“命名回调”作为 DSL 的一部分，或者生成 lambdas。第二种方法更容易，但是当你试图理解调用序列时，可能会导致一场人间地狱。问题是，我们的行为越是迫切，DSL 方法带来的好处就越少。

### 这个？！它？！

在使用 DSL 时，没有什么比失去当前“this”和“it”的含义更容易的了。如果您使用“it”作为默认的参数名称，并且可以用一个有意义的名称替换它，那么您最好这样做。最好有一点明显的代码，而不是不明显的错误。

背景的概念会让从未面对过它的人感到困惑。现在，由于您的工具包中有了“带接收器的 lambdas ”, DSL 中不太可能出现意外的方法。只需记住，在最坏的情况下，可以将上下文设置为一个变量，例如 val mainContext = this。

### 嵌套

这个问题与列表中的第一个缺点密切相关。嵌套结构中嵌套的使用将所有有意义的代码向右移动。在一定限度内，这种偏移可能是可以接受的，但是当偏移太大时，使用 lambdas 是合理的。当然，这不会降低 DSL 的可读性，但是如果 DSL 不仅包含紧凑的结构，还包含一些逻辑，这可能是一种折衷。当您使用 DSL 创建测试时(本文涵盖的情况)，这个问题并不严重，因为数据是用紧凑的结构描述的。

### 文件在哪里，李波斯基？

当您第一次尝试处理某人的 DSL 时，您几乎肯定会想知道文档在哪里。在这一点上，我相信如果你的 DSL 被其他人使用，使用示例将是最好的文档。文档本身作为额外的参考是很重要的，但是它对读者来说不是很友好。一个特定领域的从业者通常会从这个问题开始:“我调用什么来得到结果？”所以以我的经验，类似案例的例子会更好的说明问题。

## 结论

我们已经对工具进行了概述，这些工具使您能够轻松地设计自己的定制领域特定语言。我希望你现在看到它是如何工作的。欢迎在评论中提出更多的工具。

重要的是要记住 DSL 不是万能的。当然，当你得到一个如此强大的锤子时，一切看起来都像钉子，但它不是。从小处着手，为测试创建一个 DSL，从你的错误中学习，然后，一旦你更有经验，考虑其他的使用领域。

## 关于作者

我是豪尔蒙特的软件开发人员。去年，我的职责是教育中的课表编排。我在开发基于 [CUBA 平台](https://www.cuba-platform.com/) Java 框架的应用程序时应用了上述技术。我和 Spring 一起度过空闲时间，用 Kotlin DSL 开发 Telegram bot，当然还有我的妻子。