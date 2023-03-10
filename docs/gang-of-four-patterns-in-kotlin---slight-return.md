# 科特林的四人组模式-轻微回归

> 原文：<https://dev.to/lovis/gang-of-four-patterns-in-kotlin---slight-return>

很多人在 Kotlin 上读过我的文章[四人帮模式。老实说，我没想到会有这么大的反响。它被特别报道、转发和讨论。写这篇文章的时候，](https://dev.to/lovis/gang-of-four-patterns-in-kotlin/) [github 库](https://github.com/lmller/gof-in-kotlin)已经收到了 100 多颗星星。为此感谢大家！🙌

在这篇文章中，我想再次提出讨论最多的模式:**装饰器**和**生成器**。
此外，我想添加一个 Kotlin 实现的例子，这是一个到目前为止存储库中缺少的重要模式:访问者**。**

 *** * *

#### 装饰工

> GoF 中展示的装饰模式只是实现功能组合的一种非常复杂的方式

这是马里奥·富斯科给我的评论。我该说什么？我认为他是正确的。我不想使用 Mario 在他的存储库中使用的解决方案，所以我退回到“纯”Kotlin 方法。然而，[functionale](https://github.com/MarioAriasC/funKTionale)，一个为 Kotlin 添加了许多“标准”函数模式的库，表明使用 Kotlin 进行函数组合实际上是可能的，而且没有比使用扩展函数更“纯粹”的了。

下面是同一个旧的装饰器示例，但是这次使用了函数组合:

```
fun ((String) -> String).then(f: (String) -> String): (String) -> String {
    return { t -> f(this(t)) }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事情。`(String) -> String.then`意味着函数`then`被添加到所有接受`String`并返回`String`的函数中。这意味着`then`是函数的一个*扩展函数。
这里有趣的部分是`then`也将相同类型的函数作为它扩展的参数；并且它也返回相同类型的函数。
因此可以这样写:* 

```
val compose: (String) -> String = { string: String -> "$string?" }.then { string: String -> string.replace('c', 'k') }

println(compose("functional")) // will print "funktional?" 
```

Enter fullscreen mode Exit fullscreen mode

它只是将函数调用链接在一起，首先在原始值上加一个`?`，并用`k`替换所有的`c`。

使用`infix`修改器可以稍微清理一下。`infix`函数是一个不用括号或点就可以访问的函数。
一个例子是 Kotlin 的内置`to`函数，它创建了一个`Pair` :

```
val pair = "key".to("value") // this is possible
val pair = "key" to "value" // but this is nicer. it works thanks to infix 
```

Enter fullscreen mode Exit fullscreen mode

将`infix`修饰符应用到`then`函数会产生下面的代码:

```
infix fun ((String) -> String).then(f: (String) -> String): (String) -> String { ... }
...
val compose = { ... } then { ... } 
```

Enter fullscreen mode Exit fullscreen mode

为了让这个例子更类似于旧的例子，我引入了另一个`infix`扩展函数:

```
infix fun String.decorate(f: (String) -> String): Text {
    return DefaultText(f(this))
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数`decorate`是对`String`的扩展函数，它用函数`f`的结果构造了一个`DefaultText`对象。
**我现在实际上是在装饰`Strings`而不再是`Text`** 对象了，但结果是一样的。

有了这两个扩展函数，我现在可以创建并绘制一个装饰文本，如下所示:

```
 fun underline(text: String) = "_${text}_"
 fun background(text: String) = "\u001B[43m$text\u001B[0m"

 val text = "Functional" decorate (::underline then ::background)

 text.draw() 
```

Enter fullscreen mode Exit fullscreen mode

`then`函数*与`background`函数*组成`underline`。我在这里使用函数引用，(`::`)，但是 lambdas 也可以。
这是函数组合的一个非常具体的例子，如果我对它进行重构和概括，它最终将与来自[functionale 库](https://github.com/MarioAriasC/funKTionale) :
的组合相同

```
infix fun <P1, IP, R> ((P1) -> IP).andThen(f: (IP) -> R): (P1) -> R = { p1: P1 -> f(this(p1)) } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 构建器

有些人注意到我的**构建器**的例子并不地道，因为你可以简单地使用带有命名参数的构造器。我确实同意这种说法，但并不是在所有情况下都同意。
我的例子很简单；一个有两个参数的`Car`类。

```
class Car() {
    var color: String = "red"
    var doors = 3

    override fun toString() = "$color car with $doors doors"
} 
```

Enter fullscreen mode Exit fullscreen mode

但这已经是问题了。太简单了。您可以很容易地(并且更习惯地)用构造函数参数来编写它:

```
class Car(var color = "red", var doors = 3) {
    override fun toString() = "$color car with $doors doors"
}

//called e.g. like this:
val car1 = Car(color="green")
val car2 = Car(doors=4, color="blue")
val car3 = Car() 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很好的模式。对于简单的例子，它绝对可以取代**构建器**模式。但是如果你有更多的房产呢？它会变得很难看。除了视觉效果，我要说的是，一个超过 5 个构造函数参数的类几乎总是一个坏主意，因为它可能做太多的事情(除非它都是数据)。
这种方法的另一个缺点是，我不能重用和传递配置。有了`apply`，我可以准备一个这样的汽车配置:

```
val config: Car.() -> Unit = {
    color = "yellow"
    doors = 5
} 
```

Enter fullscreen mode Exit fullscreen mode

并在以后通过使用`Car().apply(config)`来应用它。我还可以一次应用多个配置，这比使用几十个构造函数参数更容易阅读代码。

```
val car = Car()
           .apply(COBALT_BLUE)
           .apply(LEATHER_PACKAGE)
           .apply(TIRES_18_INCH) 
```

Enter fullscreen mode Exit fullscreen mode

所以为了保持灵活性，在大多数情况下我仍然更喜欢`apply`方法，但是构造函数方法同样有用和有效。

* * *

#### 来访者

> 表示要在对象结构的元素上执行的操作。访问者允许你定义一个新的操作，而不改变它所操作的元素的类

当我想提供一组在相同元素上操作的不相关的算法时，一个**访问者**可以是解决方案。与例如**策略**的区别在于，被执行的操作取决于两种类型:访问者**的类型和它“访问”的元素的类型。这叫做*双调度*。这是这个模式的关键。元素不会向元素添加许多方法，因此静态地将所有可能的功能绑定到它们，元素将只声明一个方法`accept`，基本上就是说“请在我身上操作”,并利用 OOP 最重要的概念之一——动态绑定。**

不过，这也有一些暗示。虽然添加新的操作很容易，但是向元素层次结构添加新的类却相当困难。对于层次结构中的每个新类，每个访问者都需要实现一个新方法。

访问者及其对应的元素可能如下所示:

```
interface ShapeVisitor<T> {
  T visit(Square element);
  T visit(Circle element);
  T visit(Rectangle element);
  // ... and another `visit` method for every element of the hierarchy
}
interface Shape {
   <T> T accept(ShapeVisitor<T> visitor);
} 
```

Enter fullscreen mode Exit fullscreen mode

(这次我偷了马里奥·富斯科的例子。我不觉得羞耻！)

`Shape`的每个子类型都有一个方法，每个形状都有一个方法来接受算法。一个具体的访问者可能看起来像这样:

```
public static class AreaVisitor implements ShapeVisitor<Double> {
        public Double visit(Square element) {
            return element.side * element.side;
        }

        public Double visit(Circle element) {
            return Math.PI * element.radius * element.radius;
        }

        public Double visit(Rectangle element) {
            return element.height * element.width;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

并且会被这样称呼:

```
 double totalArea = 0.0;
 ShapeVisitor<Double> areaVisitor = new AreaVisitor();
 for (Shape figure : figures) {
    totalArea += figure.accept(areaVisitor);
 }
 System.out.println("Total area = " + totalArea); 
```

Enter fullscreen mode Exit fullscreen mode

要添加另一个操作，我可以简单地添加另一个**访问者**

```
public static class PerimeterVisitor implements ShapeVisitor<Double> {
        public Double visit(Square element) {
            return 4 * element.side;
        }

        public Double visit(Circle element) {
            return 2 * Math.PI * element.radius;
        }

        public Double visit(Rectangle element) {
            return (2 * element.height + 2 * element.width);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

正如其他人已经指出的，这只是在对象的类上做一个`switch`语句的一种复杂方式。Java 在类层次结构上没有`switch`。反正这被认为是反 oop 的。所以我们使用访问者模式。
函数式编程有一个更好的处理方式是这样的:*模式匹配*。

幸运的是，Kotlin 是一种多范例语言，所以我们不需要*纯粹的*面向对象。同样幸运的是，Kotlin 将“光”与`when`表达式和`sealed`类进行模式匹配。

```
sealed class Shape()
class Square(val side: Double) : Shape()
class Circle(val radius: Double) : Shape()
class Rectangle(val width: Double, val height: Double) : Shape() 
```

Enter fullscreen mode Exit fullscreen mode

密封类的好处是——不像接口或`abstract`类—
,编译器知道该类的每一种可能的表现形式。因此，可以使用一个`when`表达式来检查是否所有可能的子类都被检查过:

```
 val areaVisitor = { shape: Shape ->
        when (shape) {
            is Rectangle -> shape.height * shape.width
            is Circle -> shape.radius.square() * Math.PI
            is Square -> shape.side.square()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果我删除了行`is Square -> ...`，代码将无法编译(这类似于`enum`的行为。还要注意[智能类型转换](https://kotlinlang.org/docs/reference/typecasts.html#smart-casts)的用法，它允许在没有显式类型转换的情况下访问子类的属性(例如`radius`或`side`)。

现在，我可以使用 lambda-visitor 来总结所有区域。

```
 val totalArea = figures.sumByDouble { areaVisitor(it) }
 println("Total area = $totalArea") 
```

Enter fullscreen mode Exit fullscreen mode

添加一个新的**访问者**也很容易，只需定义一个新的 lambda。

```
val perimeterVisitor = { shape: Shape ->
        when (shape) {
            is Rectangle -> 2 * shape.height + 2 * shape.width
            is Circle -> 2 * Math.PI * shape.radius
            is Square -> 4 * shape.side
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

使用`when`语句甚至可以做更多的事情，例如一个`else`分支或者为多个条件共享同一个操作。
要了解更多信息，我建议查看关于 [`when`](https://kotlinlang.org/docs/reference/control-flow.html#when-expression) 和 [`sealed`职业](https://kotlinlang.org/docs/reference/sealed-classes.html)的文档。

我把完整的新例子添加到了 github 库。💥

* * *

<sup>封面图片取自[stock snap . io](http://stocksnap.io)T3】</sup>**