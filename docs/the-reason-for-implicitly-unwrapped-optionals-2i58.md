# 隐式展开选项的原因

> 原文：<https://dev.to/monicag/the-reason-for-implicitly-unwrapped-optionals-2i58>

在[之前的一篇文章](https://dev.to/monicag/swift-optionals-7g5)中，我写了关于隐式解包期权的文章。然而，我对自己创造的例子并不满意；感觉很做作。下面是我用的例子:

```
let myMailBox = MailBox()
myMailBox.mail = Mail(to: "Alice", from: "Bob")

let myMailImplicitlyUnwrapped: Mail! = myMailBox.mail

print(myMailImplicitlyUnwrapped.to) // the ! is not needed
print(myMailImplicitlyUnwrapped.from) 
```

Enter fullscreen mode Exit fullscreen mode

这个代码似乎不现实。这给我留下了几个问题。我什么时候会创建这样的代码？在什么情况下我会创建`myMailImplicitlyUnwrapped`常量？为什么我不把`Mail(to: "Alice", from: "Bob")`赋给常量呢？例子:

```
let myMail: Mail =  Mail(to: "Alice", from: "Bob") 
print(myMail.to) 
print(myMail.from) 
```

Enter fullscreen mode Exit fullscreen mode

我想更多地了解隐式解包选项。它们用在哪里，用途是什么？

经过一些研究，我发现了两个用例场景:

*   声明一个`@IBOutlet`
*   避免*参考周期*

在这两种情况下，他们的目的都是创建更简单的代码。

## @IBOutlet

声明一个`@IBOutlet`似乎是主要的用例。从[开始开发 iOS 应用(Swift)](https://developer.apple.com/library/content/referencelibrary/GettingStarted/DevelopiOSAppsSwift/ConnectTheUIToCode.html) :

> 当从故事板加载视图控制器时，系统实例化视图层次结构，并将适当的值分配给所有视图控制器的出口。调用视图控制器的 viewDidLoad()方法时，系统已经为控制器的所有出口分配了有效值，您可以安全地访问它们的内容。

由于插座将在初始化后设置，我们希望将其用作非可选属性。如果出口是可选的，那么任何使用它的代码都需要首先打开它。将 outlet 声明为隐式展开的可选函数意味着任何调用代码都可以直接访问它。这导致了更简单的代码。

## 避免参考周期

引用循环是指两个类包含对彼此的引用。如果类之间包含[强引用](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html#//apple_ref/doc/uid/TP40014097-CH20-ID51)，就会发生内存泄漏。发生泄漏是因为 ARC 无法从内存中删除这些类，即使它们不再被使用。这是因为每个类都有一个引用另一个类的属性。

创建参考周期有几种方法，每种方法都有自己的解决方案。要考虑的一个场景是初始化后两个属性都不能是`nil`。 [Swift 文档](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html)描述了这种情况:

> 然而，还有第三种情况，在这种情况下，两个属性都应该有一个值，并且一旦初始化完成，任何一个属性都不应该为空。在这种情况下，将一个类上的无主属性与另一个类上的隐式取消包装的可选属性结合起来是很有用的。
> 
> 这使得一旦初始化完成，就可以直接访问这两个属性(没有可选的展开)，同时仍然避免引用周期。

苹果文档中的例子:

```
class Country {
    let name: String
    var capitalCity: City!
    init(name: String, capitalName: String) {
        self.name = name
        self.capitalCity = City(name: capitalName, country: self)
    }
}

class City {
    let name: String
    unowned let country: Country
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

将`capitalCity`声明为隐式展开的可选值意味着它最初是`nil`。`nil`值允许`Country`类初始化并将`self`传递给`City`初始化器。在`Country`和`City`被初始化后，`capitalCity`将具有非`nil`值。现在，任何使用`capitalCity`的代码都可以直接访问它。如果`capitalCity`是可选的，使用时需要打开。但是作为一个隐式展开的选项，这是不需要的。因此，隐式取消包装的可选方法再次简化了代码的操作。

感谢您的阅读！如果我错过了隐式展开选项的案例，请让我知道！

## 参考文献

*   为什么要创建“隐式解包选项”？
*   [开始开发 iOS 应用程序(Swift)“为 UI 元素创建出口”部分，第 10 点](https://developer.apple.com/library/content/referencelibrary/GettingStarted/DevelopiOSAppsSwift/ConnectTheUIToCode.html)
*   [Swift 编程语言(Swift 4)指南中的无主引用和隐式展开的可选属性部分](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html)