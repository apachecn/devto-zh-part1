# 面向网络开发者的 Swift

> 原文：<https://dev.to/restoreddev/swift-for-web-developers-2hh>

Swift 已经成为 iOS 开发的标准语言，也正在成为服务器端编程的流行语言。我以前的编程经验主要是 PHP 和 JavaScript，所以当我开始时，Swift 是一个全新的世界。我写这篇文章是为了给来自 PHP、Python 或 Ruby 的 web 开发人员一个如何使用 Swift 的开端。

## 安装

### MacOS

在 Mac 上安装 Swift 非常简单。只需安装 [Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) ！它有一个很好的试验语言的功能。

### Linux

要在 Linux 上使用 Swift，只需按照[Swift.org](https://swift.org/getting-started/)上的说明下载并运行编译器。或者，一个 Docker [映像](https://hub.docker.com/_/swift/)可用于运行 Swift。

### 窗口

如果你使用的是 Windows 10，你可以在 Linux 子系统中安装 Swift，但最简单的方法是安装上面提到的 Docker 镜像。

## 我的分号在哪里？

```
print("Hello, world!") 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到的第一件事是，Swift 不需要在每一行的末尾使用分号，类似于 Ruby 或 Python。Swift 构建时考虑了脚本语言和编译语言，因此您将看到这两种语言的不同特性。

此外，与 Ruby 和 Python 类似，在条件语句中不必使用括号，但是可以像 Java 和 PHP 一样使用括号将块的内容括起来。

```
if true {
    print("It is true")
} 
```

Enter fullscreen mode Exit fullscreen mode

Swift 对循环使用相同的结构。

```
let fruits = ["Apple", "Pear", "Orange"]
for fruit in fruits {
    print("My favorite fruit is: \(fruit)")
} 
```

Enter fullscreen mode Exit fullscreen mode

## let vs var

在 Swift 中，数据名可以用两个不同的词来声明:`let`和`var`。与 ES6 JavaScript 不同，`let`是常量，`var`是常规变量。`let`常量的值在设置后不能更改，但`var`可以根据需要更改。

如果你写下面的代码，你会得到一个错误:

```
let name = "Andrew"
name = "not Andrew" 
```

Enter fullscreen mode Exit fullscreen mode

但是，以下是可以接受的:

```
var count = 1
count = 2 
```

Enter fullscreen mode Exit fullscreen mode

如果您创建了一个变量，然后从未更改过它，Swift 编译器会给你一个警告，所以最好的做法是从一个常量开始，只有当它需要变成其他东西时才将其更改为变量。

## 雨燕喜欢的类型

Swift 中的一切都是静态类型的，但类型是像 Go 或 Rust 那样推断出来的。类型推断基本上意味着编译器根据变量的初始值计算出变量的类型。因此，如果你试图创建一个没有定义类型的常量或变量，你必须立即设置它的值，这样编译器就知道它是什么类型。

例如，如果您运行这段代码，您将会得到一个错误。

```
let favoriteMovie
favoriteMovie = "Return of the Jedi" 
```

Enter fullscreen mode Exit fullscreen mode

您必须立即声明常量类型或设置常量。

```
let favoriteMovie: String
favoriteMovie = "Return of the Jedi"

let secondFavoriteMovie = "The Empire Strikes Back" 
```

Enter fullscreen mode Exit fullscreen mode

Swift 拥有所有标准数据的类型:`Int` `String` `Float` `Double` `Bool`等等。

## 参数名称！

我最喜欢的 Swift 特性之一是参数命名。当您创建一个函数并命名参数时，调用该函数的代码也必须命名参数。

```
func sayHello(greeting: String) {
    print(greeting)
}

sayHello(greeting: "Hola!") 
```

Enter fullscreen mode Exit fullscreen mode

一旦你习惯了，它会让你的功能更有表现力。如果不想有命名参数，可以在名字前面加一个`_`省略。

```
func sayGoodbye(_ goodbye: String) {
    print(goodbye)
}

sayGoodbye("Adios!") 
```

Enter fullscreen mode Exit fullscreen mode

在 Swift 标准库中，您将看到根据具体情况以两种方式编写的函数，因此这是编写您自己的函数的良好参考。

## 问号是怎么回事？

如果你以前读过任何 Swift 代码，你可能会注意到几个使用问号的例子。问号用来创建一个特殊的类型，它基本上意味着值可以是一个类型或零。例如，如果一个变量像`var optional: String?`一样被声明，这意味着`optional`可以是`String`或`nil`。

现在，期权需要更多的工作来获得价值。要使用可选类型的变量，您必须*解开*值。

```
let optional: String? = "I am an optional"
if let value = optional {
    print(value)
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用 if 语句，您验证了可选的值不是`nil`,如果是，它被设置为`value`,以便在`if`块中使用。

您也可以使用 guard 陈述式来解开选择性的。

```
enum OptionalError: Error {
    case optionalIsNil
}

let value: String? = nil
guard let name = value else {
    throw OptionalError.optionalIsNil
} 
```

Enter fullscreen mode Exit fullscreen mode

一个 guard 语句试图将值展开到`name`常量中，但是如果它返回`nil`，它将运行该块的内容。在这种情况下，因为`value`是`nil`，所以保护块抛出一个客户错误，指示出现了问题。

第一次使用选项时，你可能会感到困惑，你可能会问，为什么要使用它们呢？可选的目标是确保你的代码处理每一种情况，其中你的常量和变量可能是`nil`。假设您有一个从会话中获取当前用户的函数，您将希望处理会话为空的情况。

```
func getCurrentUser(session: Session) -> User? {
    if session.has("currentUser") {
        return session.get("currentUser")
    }

    return nil
}

let session = Session()
if let user = getCurrentUser(session: session) {
    print(user.name)
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`getCurrentUser`可能会返回`nil`,所以如果从函数返回的值实际上是一个用户对象，我们就只打印用户名。如果我们不检查`nil`，我们会有一个运行时错误，因为没有名字存在。最后，选项的目标是让你的代码更加正确。

## 结构 vs 类

当您开始在 Swift 中编码时，您会注意到有两个看起来几乎相同的构造。这里有一个例子:

```
struct CarSize {
    var height: Double
    var width: Double
    var length: Double

    init(height: Double, width: Double, length: Double) {
        self.height = height
        self.width = width
        self.length = length
    }

    func getArea() -> Double {
        return width * length
    }
}

class Car {
    var make: String
    var model: String
    var color: String

    init(make: String, model: String, color: String) {
        self.make = make
        self.model = model
        self.color = color
    }

    func getType() -> String {
        return "\(make): \(model)"
    }
}

let carSize = CarSize(height: 10, width: 10, length: 20)
let car = Car(make: "Chevrolet", model: "Corvette", color: "Red") 
```

Enter fullscreen mode Exit fullscreen mode

结构和类都用于创建可以包含属性和方法的自定义类型。那么区别在哪里呢？

首先，只有一个类可以从另一个类继承。结构不能从结构继承。如果你需要任何类型的继承，那么你必须使用一个类。

其次，类的实例通过引用传递，结构的实例通过值传递。基本上，这意味着它们在内存中的存储方式不同。当您创建一个类的实例时，保存该实例的变量实际上并不包含该实例，它包含一个内存 id，该 id 是对该实例在内存中的位置的引用。保存结构实例的变量实际上保存的是结构，而不仅仅是引用。因此，当您将一个类实例传递给一个函数时，该函数接收一个对对象的引用，但是当您将一个结构实例传递给一个函数时，它接收该结构的一个*副本*。这两种情况各有利弊，因此选择结构还是类取决于您要完成的任务。

最后，结构通常用于包装简单的数据值，如示例中的`CarSize`,而类用于任何可能包含经常变化的类型或具有可变方法的类型。Apple 建议在大多数情况下使用类。

## 结论

我希望这份快速摘要能帮助您更快地理解 Swift 的一些概念。我推荐阅读苹果的[Swift 编程语言](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/)一书，了解更多。如果你有任何问题，请发表评论。

快乐的雨燕！