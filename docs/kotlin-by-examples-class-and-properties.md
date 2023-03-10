# Kotlin 示例:类和属性

> 原文：<https://dev.to/dbottillo/kotlin-by-examples-class-and-properties>

在过去的几个月里，我一直在使用 Kotlin，我对它非常满意！对于不知道它的人来说，这是一种由 JetBrains(IntelliJ 背后的公司，因此是 Android Studio)构建的语言，它编译成 Java 的相同 JVM 字节码。

这是一种非常现代和漂亮的语言，可以和 Java 文件一起使用，所以不需要转换整个项目。

在这篇博文中，我将向你展示如何用 Kotlin 编写你通常在 Android/Java 项目中编写的内容，以及为什么用 Kotlin 编写代码更快更好:=)。

让我们从 Java 中一个类的标准定义开始:

```
public abstract class Car {
    private String name;
    Car(String name){
        this.name = name;
    }
}
public class Ford extends Car{
    private String model;
    private String color;

    Ford(String model, String color){
        super("Ford");
        this.model = model;
        this.color = color;
    }
}
Ford focus = new Ford("Focus", "blue") 
```

Enter fullscreen mode Exit fullscreen mode

非常标准的 Java 类，我们有一个基础抽象类叫做“Car ”,还有一个扩展 Car 的适当类“Ford”。在 Kotlin 中，相同的代码应该是:

```
abstract class Car(val name: String)

class Ford(val model: String, val color: String) : Car("Ford")

val focus = Ford("Focus", "Blue") 
```

Enter fullscreen mode Exit fullscreen mode

所以 15 行 java 代码只有 3 行！在 Kotlin 中，您可以在类定义中内联定义构造函数，并且可以根据字段的可见性免费获得 getters 和 setters(稍后将详细介绍)。Java 的关键字“extends”被替换成了一个“: ”,你也可以内联抽象类的构造函数(在这里是:Car(“Ford”))。

```
Ford focus = new Ford("Focus", "blue")
focus.getName()    -> java way
val focus = Ford("Focus", "Blue")
focus.name         -> kotlin way 
```

Enter fullscreen mode Exit fullscreen mode

构造函数和方法都支持默认值，这是 Java 做不到的:

```
class Ford(val model: String, val color: String = "Blue") : Car("Ford")
val focus = Ford("Focus")
println(focus.color)          -> prints Blue 
```

Enter fullscreen mode Exit fullscreen mode

在创建一个对象时也可能有命名字段，这解决了我认为 Java 的一个主要问题:理解用于创建一个新对象的字段的含义。
所以让我们想象一个用 Java 编写的 Book 类，它有一个参数作为字符串:

```
Book book = new Book("George") 
```

Enter fullscreen mode Exit fullscreen mode

“乔治是什么？是作者？书的名字？书的主人？在 Kotlin 中，您可以这样做:

```
Book book = new Book("George")     -> Java
val book = Book("George")          -> Kotlin like Java
val book = Book(author="George")   -> more expressive way 
```

Enter fullscreen mode Exit fullscreen mode

肯定更好！

在 Kotlin 中，您可以在类的开头添加关键字“data ”,如果您这样做，编译器会添加这些“免费”的方法:

*   `equals()` / `hashCode()`对，
*   `toString()`的形式“福特(名称=福特，型号=福克斯)”。
*   `componentN()`
*   `copy()`

因此，只要一个关键字，你就可以免费获得大量代码！我想向你展示一下`componentN()`函数的含义，因为这是你在 Java 中无法做到的。这个想法是你可以破坏你的类的声明:

```
val focus = Ford("Focus", "Blue")
val (model, color) = focus
println("$model and $color")   -> this will print: "Focus and Blue" 
```

Enter fullscreen mode Exit fullscreen mode

此外，复制方法对于从先前的对象创建另一个对象非常有用:

```
val focus = Ford("Focus", "Blue")
val yellowFocus = focus.copy(color = "Yellow") 
```

Enter fullscreen mode Exit fullscreen mode

在 Kotlin 中，属性可以是 var 或 val。你可以把 val 看作是 Java 中的一个 final 字段(所以它不能被修改),并把它看作一个不能为 null 的可变属性，如果你想要一个可变的可为 null 的属性，你需要用？表情:

```
var mutable = "mutable"
val immutable = "immutable"
mutable = "changed"                    -> 'it compiles'
immutable = "changed"                  -> 'doesn't compile'
var mutableCanBeNull: String? = null   -> 'can be null' 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用关键字安全地访问可以为 null 的属性。:

```
var mutable = "mutable"
println(mutable.legnth)                 -> 'print 7'
var mutableCanBeNull: String? = null
println(mutableCanBeNull?.length)       -> 'does not crash! print null' 
```

Enter fullscreen mode Exit fullscreen mode

在 Android 中，大多数时候你需要等待来自框架的回调来创建对象，这意味着它们需要被定义为 null，直到框架准备好，然后每次你使用这些对象时，你需要检查它是否不是 null。

Kotlin 用 var 属性的 lateinit 关键字解决了这个问题:

```
lateinit var name: String     -> 'without lateinit it does not compile'
name = "Tom"
println(name.length) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您不需要做“name”。但只是“name ”,因为 lateinit 关键字告诉编译器，在使用它之前，您将填充该属性！

暂时就这样吧！在下一篇文章中，我将讨论函数定义、返回类型、回调和静态字段。