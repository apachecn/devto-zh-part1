# 理解 Kotlin 中的可空性

> 原文：<https://dev.to/adammc331/understanding-nullability-in-kotlin>

每个 Java 程序员在他们人生的某个阶段都会面临可怕的`NullPointerException`。有时候是你的错，有时候是讨厌的比赛状态。不管怎样，这是一个头疼的问题，通常会导致代码中出现大量的`if (myVariable != null) { }`条件。然而，最近的热潮[科特林](https://kotlinlang.org)也能对此有所帮助。科特林在它的类型系统中引入了[无效安全](https://kotlinlang.org/docs/reference/null-safety.html)，有可能移除所有的 npe。

这篇文章既回顾了上面链接的官方文档(基本相同，有稍微深入的解释)，也在最后提供了一些处理可空性的通用技巧和窍门——这对许多 Java 程序员来说是一种新的语言。

# 可空和不可空类型

首先，让我们解释一下我们一直说 Kotlin 在类型系统中具有可空性是什么意思。在 Java 中，我们知道一个对象既可以有值，也可以为空。正如第一段所讨论的，这可能会导致麻烦。然而，在 Kotlin 中，我们可以说一个类型永远不会为空。我们通过简化指定类型来做到这一点。如果我们想声明一个类型为可空，我们在末尾附加一个问号。这里有一个例子:

```
 // String is a non-nullable type, so if we tried to assign null to it, there would be a compilation error
    val a: String = "test"
    a = null // Compilation error

    // String? is a nullable type, so if we tried to assign null to it, it would accept it
    val b: String? = "test"
    b = null // Okay 
```

Enter fullscreen mode Exit fullscreen mode

注意:如果您使用的是 Java interrop，那么第一个选项仍然存在抛出 NPE 的风险。我们将在后面的平台类型中讨论这一点。

# 检查可空性

有几种方法可以检查和处理类型的可空性。让我们用上面的`String?`例子来讨论每一个，我们想用字符串长度来控制流。

## 显式检查

就像 Java 一样，我们可以显式地检查 null:

```
 val b: String? = "test"

    if (b != null && b.length > 0) {
        // Do something with the string
    } else {
        // String is null, handle error
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 安全操作员

如果我们不想处理上面代码的冗长，我们可以使用 Kotlin 提供的安全操作符(`?.`)。如果值为非空，该运算符将返回该值，如果无法读取该值，则返回空值。下面是使用和不使用安全操作符时的代码:

```
 val length: Int? = if (b != null) b.length else null
    val length: Int? = b?.length 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，安全操作符使代码更加简洁。当您有许多嵌套对象，并且您能够将多个安全操作符调用链接在一起时，这将变得更加有用。

## 猫王符

在 Java 中，我们可能还习惯于对 null 进行显式检查，如果没有找到，则返回另一个值。举个例子，你这辈子可能写过这样的代码:

```
 return (myValue != null) ? myValue : -1; 
```

Enter fullscreen mode Exit fullscreen mode

Kotlin 提供了一个 elvis 操作符(`?:`)，其工作方式类似于 safe 操作符。然而，它不是返回 null，而是返回您提供的默认值。上面的 Java 代码可以用 Kotlin 写成:

```
 return myValue ?: -1 
```

Enter fullscreen mode Exit fullscreen mode

以字符串长度为例:

```
 val length: Int = b?.length ?: 0
    if (length > 0) doSomething() else fail() 
```

Enter fullscreen mode Exit fullscreen mode

## 安全类铸造

类似于类型的安全操作符，我们可以使用`as?`操作符来安全地转换对象。如果强制转换不成功，它将返回 null:

```
 val intVal: Int? = myValue as? Int 
```

Enter fullscreen mode Exit fullscreen mode

## 请勿输入

如果出于某种原因，你坚持要有一个`NullPointerException`，你可以使用[！！](https://kotlinlang.org/docs/reference/null-safety.html#the--operator)如果你试图访问一个空对象，这个操作符将抛出一个 NPE。

# 平台类型

我在上面提到过，即使你声明一个类型为不可空，如果它与 Java 代码交互，你也可以有一个 NPE。嗯，[正如 Kotlin 文档所说的](https://kotlinlang.org/docs/reference/java-interop.html#null-safety-and-platform-types)，Java 中的任何对象都可能是 null，所以在 Kotlin 中继承它的类型安全是不切实际的。出于这个原因，Java 库返回的任何对象都被称为“平台类型”，对于这些类型，空检查是宽松的，因为不能保证为空。

然而，如果 Java 库使用了`@Nullable`或`@NotNull`注释，Kotlin 编译器将能够推断出要使用的类型安全。

# 要寻找的东西

读完这篇文章后，不要只是浏览你所有的 Kotlin 代码，让所有的代码都为空，以为你已经避开了所有的 npe，你就大功告成了。在使一个类型成为可选类型之前，您应该考虑几件事情:

1.  这种类型为空有意义吗？如果你希望你总是有这个值，那么就让它不可空，这样可以避免不必要的 safe 和 elvis 操作符调用。
2.  这个值是来自外部 Java 库，还是由外部 Java 库设置的？如果您的答案是肯定的，那么使用可选的。如果外部库不能保证安全，你的代码也不能。

## 懒惰的委托财产

你也应该知道 Kotlin 的一些[委托属性](https://kotlinlang.org/docs/reference/delegated-properties.html)，尤其是`lazy()`方法。在我们讨论它的作用之前，让我们解释一下我们试图解决的问题。考虑下面的片段代码:

```
 class MyFragment : Fragment() {
        private var manager: MyAPIManager? = null

        @Override
        public void onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)

            manager = MyAPIManager(context)
            manager.authorize()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们在类级别定义了一个管理器对象。我们必须给它一个初始值，我们被迫给它空值。这是因为此时我们无法访问上下文，所以我们别无选择，只能在 onCreate 方法中给它赋值。不管这种方法看起来多么合乎逻辑，它有两个后果:

1.  我们被迫将 manager 声明为可空，即使我们*知道*一旦它在 onCreate 中被赋值就不会为空。
2.  我们必须将它标记为`var`(使其可变)，这样我们就可以在 onCreate 内部对它赋值，尽管我们确实知道它不应该被重新分配给任何其他对象。

解决方案？[懒惰的属性](https://kotlinlang.org/docs/reference/delegated-properties.html#lazy)。该属性接受一个 lambda，该 lambda 在第一次访问该属性时执行。之后，它将返回分配给它的值。这样，我们可以将属性声明为不可变的、非空的，这样，只要片段是在我们第一次访问它之前创建的，我们就可以避免上面提到的两个问题。下面是代码现在的样子:

```
 class MyFragment : Fragment() {
        private val manager: MyAPIManager by lazy {
            MyAPIManager(context)
        }

        @Override
        public void onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)

            manager.authorize()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

## Lateinit 属性

与 lazy 类似，我们可以使用 [lateinit](https://kotlinlang.org/docs/reference/properties.html#late-initialized-properties) 关键字来定义将在构造函数外部初始化的属性。语法看起来类似于 lazy，但是你仍然需要在某些时候初始化它:

```
 class MyFragment : Fragment() {
        lateinit var manager: MyAPIManager

        @Override
        public void onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)

                        manager = MyAPIManager(context)
            manager.authorize()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们仍然可以将变量声明为不可空，并在适当的时候给它赋值。但是，在分配之前，我们仍然无法访问该字段。但是，如果我们这样做了，就会抛出一个异常，解释该字段尚未初始化，这与 NPE 略有不同。

我希望您发现这是一个关于 Kotlin 可空性的有用指南，并且您能够看到它提供的好处，何时使用它，以及如何使用它为您带来好处。您以前在 Kotlin 中使用空性时遇到过困难吗，或者知道 lazy/lateinit 属性之外的其他技巧吗？请在评论中告诉我们！