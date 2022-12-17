# 科特林的四人帮模式

> 原文：<https://dev.to/lovis/gang-of-four-patterns-in-kotlin>

科特林越来越重要了。用 Kotlin 实现的常见设计模式会是什么样子？
受到马里奥·富斯科的[演讲](https://www.youtube.com/watch?v=Rmer37g9AZM) / [博文](https://www.voxxed.com/blog/2016/04/gang-four-patterns-functional-light-part-1/) / [资源库](https://github.com/mariofusco/from-gof-to-lambda)“从 GoF 到 lambda”的启发，我决定在 Kotlin 中实现一些计算机科学中最著名的设计模式！

然而，目标不是简单地*实现*模式。由于 Kotlin 支持面向对象编程，并且可以与 java 互操作，我可以复制-自动转换 Mario 库中的每个 Java 类(不管是“传统”还是“lambda”示例)并且**它仍然可以工作！**
同样值得注意的是，这些模式被发现是为了克服 90 年代命令式编程语言(尤其是 C++语言)的缺点。许多现代语言都提供了克服这些问题的特性，而无需编写额外的代码或退回到模式。

这就是为什么——就像`g ∘ f`仓库中的 Mario 我将试图找到一种更简单、更容易或更惯用的方法来解决每个模式正在解决的相同问题。

如果不喜欢看解释，可以直接跳转到我的 [github 资源库](https://github.com/lmller/gof-in-kotlin)

* * *

你可能知道，有三种类型的(gof)模式:**结构**、**创造**和**行为**模式。首先是结构模式。这很难，因为结构模式是关于结构的！我如何实现一个与*不同的*结构？我不能。一个例外是**装饰器**。虽然它在技术上只是关于结构，但它的用法更多的是关于行为或责任。

### 结构模式

#### 装饰工

> 动态地将附加责任附加到对象上

假设我想用一些文本效果来装饰一个类`Text`:

```
class Text(val text: String) {
    fun draw() = print(text)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你知道模式，你也知道为了“装饰”(也就是扩展行为)类，必须创建一组类。在 Kotlin 中，这些额外的类可以通过使用*扩展函数* :
来避免

```
fun Text.underline(decorated: Text.() -> Unit) {
    print("_")
    this.decorated()
    print("_")
}

fun Text.background(decorated: Text.() -> Unit) {
    print("\u001B[43m")
    this.decorated()
    print("\u001B[0m")
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这些扩展函数，我现在可以创建一个新的`Text`并修饰它的`draw`方法，而不用创建其他类:

```
Text("Hello").run {
    background {
        underline {
            draw()
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您从命令行运行此命令，您将看到带有彩色背景的文本“_Hello_ ”(如果您的终端支持 ansi 颜色)。与最初的装饰器模式相比，有一个缺点:我不能传递“预先装饰”的对象，因为不再有装饰器类了。为了解决这个问题，我可以再次使用函数。函数在科特林是一等公民，所以我可以把它们传来传去。

```
fun preDecorated(decorated: Text.() -> Unit): Text.() -> Unit {
    return { background { underline { decorated() } } }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 创作

#### 构建器

> 将复杂对象的构造与其表示分离，以便同一构造过程可以创建不同的表示

**构建器**模式非常有用。我可以避免变量繁重的构造函数，轻松重用预定义的设置。Kotlin 通过`apply`扩展函数支持这种开箱即用的模式。
考虑一类`Car` :

```
class Car() {
    var color: String = "red"
    var doors = 3
} 
```

Enter fullscreen mode Exit fullscreen mode

我现在可以使用`apply` ( `also`也可以)扩展来初始化汽车:
，而不是为这个类创建一个单独的`CarBuilder`

```
Car().apply {
    color = "yellow"
    doors = 5
} 
```

Enter fullscreen mode Exit fullscreen mode

因为函数可以存储在变量中，所以初始化也可以存储在变量中。这样，我可以预先配置**构建器**功能，例如`val yellowCar: Car.() -> Unit = { color = "yellow" }`

#### 原型

> 使用原型实例指定要创建的对象种类，并通过复制这个原型来创建新对象

在 Java 中，理论上可以使用`Cloneable`接口和`Object.clone()`实现原型。但是， [`clone`坏了](http://www.artima.com/intv/bloch13.html)，要避免。
Kotlin 用数据类解决了这个问题。
使用数据类时，免费获得`equals`、`hashCode`、`toString`、`copy`。通过使用`copy`，可以克隆整个对象，并有选择地改变新对象的一些属性。

```
data class EMail(var recipient: String, var subject: String?, var message: String?)
...

val mail = EMail("abc@example.com", "Hello", "Don't know what to write.")

val copy = mail.copy(recipient = "other@example.com")

println("Email1 goes to " + mail.recipient + " with subject " + mail.subject)
println("Email2 goes to " + copy.recipient + " with subject " + copy.subject) 
```

Enter fullscreen mode Exit fullscreen mode

#### 单胎

> 确保一个类只有一个实例，并提供对它的全局访问点

虽然 **Singleton** 目前被认为是一种反模式，但它有它的用法(我在这里不讨论这个话题，只是谨慎使用)。在 Java 中创建一个 **Singleton** 需要很多仪式，但是 Kotlin 用 *`object`声明*让它变得很容易。

```
object Dictionary {
    private val definitions = mutableMapOf<String, String>

    fun addDefinition(word: String, definition: String) {
        definitions.put(word.toLowerCase(), definition)
    }

    fun getDefinition(word: String): String {
        return definitions[word.toLowerCase()] ?: ""
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里使用关键字`object`将自动创建一个类`Dictionary`和它的一个实例。实例是延迟创建的，所以直到实际使用时才创建。
像 java 中的静态函数一样访问对象:

```
val word = "kotlin"
Dictionary.addDefinition(word, "an awesome programming language created by JetBrains")
println(word + " is " + Dictionary.getDefinition(word)) 
```

Enter fullscreen mode Exit fullscreen mode

### 行为的

#### 模板法

> 在操作中定义算法的框架，将一些步骤推迟到子类

这种模式也利用了类的层次结构。您定义了一个`abstract`方法，并在基类内部的某个地方调用它。实现由子类处理。

```
//java
public abstract class Task {
        protected abstract void work();
        public void execute(){
            beforeWork();
            work();
            afterWork();
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我现在可以派生一个具体的`Task`，它实际上在`work`中做一些事情。
类似于**装饰者**的例子如何使用扩展函数，我的**模板方法**方法使用了一个顶级函数。

```
//kotlin
fun execute(task: () -> Unit) {
    val startTime = System.currentTimeMillis() //"beforeWork()"
    task()
    println("Work took ${System.currentTimeMillis() - startTime} millis") //"afterWork()"
}

...
//usage:
execute {
    println("I'm working here!")
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，根本不需要上课！有人可能会说，这类似于**策略**模式，这可能是正确的。不过话说回来，**策略**和**模板方法**都在解决非常相似的问题(如果不是相同的话)。

#### 策略

> 定义一系列算法，封装每一个算法，并使它们可以互换

假设我有一个每月支付一定费用的`Customer`。这个费用可以打折。我使用了**策略**模式，而不是每个折扣都有一个子类`Customer`-*策略*。

```
class Customer(val name: String, val fee: Double, val discount: (Double) -> Double) {
    fun pricePerMonth(): Double {
        return discount(fee)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我使用了一个函数`(Double) -> Double`来代替策略的接口。为了使这更特定于领域，我还可以声明一个类型别名，而不会失去高阶函数的灵活性:`typealias Discount = (Double) -> Double`。
无论哪种方式，我都可以定义多种策略来计算折扣。

```
val studentDiscount = { fee: Double -> fee/2 }
val noDiscount = { fee: Double -> fee }
...

val student = Customer("Ned", 10.0, studentDiscount)
val regular = Customer("John", 10.0, noDiscount)

println("${student.name} pays %.2f per month".format(student.pricePerMonth()))
println("${regular.name} pays %.2f per month".format(regular.pricePerMonth())) 
```

Enter fullscreen mode Exit fullscreen mode

#### 迭代器

> 提供一种方法来顺序访问聚合对象的元素，而不暴露其底层表示。

编写一个**迭代器**是一项罕见的任务。大多数时候，包装一个`List`并实现`Iterable`接口会更容易、更方便。
在科特林中，`iterator()`是一个运算符函数。这意味着当一个类定义一个函数`operator fun iterator()`时，可以使用一个`for`循环来迭代它(不需要接口)。这非常酷，因为它还支持扩展功能。没错——通过使用一个扩展函数，我可以使每个对象都是可迭代的。考虑这个例子:

```
class Sentence(val words: List<String>)
...
operator fun Sentence.iterator(): Iterator<String> = words.iterator() 
```

Enter fullscreen mode Exit fullscreen mode

我现在可以迭代一个`Sentence`。如果我不是该类的所有者，这也适用。

### 更多模式

我提到了一些模式，但这些并不都是四人组的模式。正如我在介绍中所说的，特别是结构模式很难或者不可能用不同于 Java 的方式编写。[其他一些模式可以在资源库](https://github.com/lmller/gof-in-kotlin)中找到。我欢迎反馈和拉动请求☺.

我希望这篇文章能让你了解 Kotlin 是如何用不同的方法来解决众所周知的问题的。

一定要看看这篇文章的第二部分:[科特林的四人组模式——轻微回归](https://dev.to/lovis/gang-of-four-patterns-in-kotlin---slight-return)

我想提到的最后一件事是，库中 java 与 kotlin 的比率是~⅓ Kotlin 和~⅔ Java，尽管两个版本做的是同样的事情🙃

* * *

<sup>封面图片取自[stock snap . io](http://stocksnap.io)T3】</sup>