# Swift 选项

> 原文：<https://dev.to/monicag/swift-optionals-7g5>

在[之前的](https://dev.to/monicag/my-swift-journey---the-basics-bn3)帖子中，我开始了对 Swift 的深入研究，介绍了基础知识:语法、范围和元组。我决定接下来处理期权。这导致了大量的调查。但是，我现在对什么是期权以及在哪里使用期权有了更好的理解。这篇文章是关于我所学到的。

## 什么是可选的？

可选的表示值可能存在或不存在的情况。可选的由符号`?`表示。

示例:

```
var a: Int? 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，变量`a`的类型是可选的整数。任何类型都可以通过添加一个`?`成为可选类型。

在这里，`a`不具有值；就是`nil`。Swift 使用`nil`来表示没有值。`?`是一个提醒开发者要小心的指示器。任何使用`a`的代码都需要处理一个可能的`nil`值。

## 为什么要用 optionals？

为什么需要选装件？为了帮助解释，我将使用邮箱和邮件作为例子。邮箱中可能有也可能没有邮件。在这种情况下，邮件是可选的；它存在或不存在。

下面，我用 Java 创建了不使用选项的邮箱和邮件类。为了简单起见，我做了以下限制:

*   Mail 使用`Strings`作为收件人和发件人地址。我没有包括街道、城市等字段。
*   一个邮箱只能包含一封邮件。(是的，在现实生活中，一个邮箱可以包含许多邮件。但是为了简单起见，在这个例子中，我将它限制为一封邮件。)

```
public class Mail {

    private String from;
    private String to;

    public Mail(String from, String to) {
        this.from = from;
        this.to = to;
    }

    public String getFrom() {
        return from;
    }

    public String getTo() {
        return to;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public class MailBox {

    private Mail mail;

    /**
     *
     * @return - null if the MailBox does not have Mail
     */
    public Mail getMail() {
        return mail;
    }

    public void setMail(Mail mail) {
        this.mail = mail;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

任何使用这段代码的人都需要知道可能返回`null`的方法。一个可能的解决方案是记录代码。然而，依赖文档有两个潜在的问题:

1.  代码没有记录。
2.  文档不正确:
    1.  可能曾经是正确的。但是，代码改变了，文档没有更新。
    2.  开发者可能没有考虑所有可能的返回情况。

给定上面的代码，有人可能会编写如下的方法:

```
void checkMail(MailBox mailBox) {
    if(mailBox != null) {
        Mail mail = mailBox.getMail();
        if (mail != null) {
            System.out.println("Mail from: " + mail.getFrom().trim());
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，`checkMail`代码可能看起来不错。但是，这里面有一个 bug。方法可以返回一个 T2。因此，使用`trim()`会导致该场景出现空指针异常。哦，不！

选项防止了上述情况，因为它们的使用说明了一个值是否可以是`nil`。非可选类型不能有`nil`值。这意味着开发人员不需要依赖文档或阅读代码来判断一个值是否可以被`nil`。该类型表明`nil`值是否可能。

Swift 和 Java(从 1.8 版本开始)都支持选项。下面，邮箱类比在 Swift 中使用选项进行了改写。

```
class Mail {
    let to: String
    let from: String

    init(to: String, from: String) {
        self.to = to
        self.from = from
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个版本的邮件类中，`to`和`from`字段不是可选的。这意味着将值设置为`nil`会导致编译器错误。

```
class MailBox {
    var mail: Mail?

} 
```

Enter fullscreen mode Exit fullscreen mode

在这个版本的 MailBox 类中，`mail`属性是一种可选的邮件类型。可能是`nil`。我们不需要依赖文档来传达这一事实。

```
import Foundation
func checkMail(mailBox: MailBox) {
    if mailBox.mail != nil {
        print("Mail from: \(mailBox.mail!.from.trimmingCharacters(in: .whitespacesAndNewlines))")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个版本中，没有对 mailBox 参数执行`nil`检查，因为它不是可选的。肯定是存在的。检查`mail`属性的`nil`值，因为它是可选的。

## Swift 和 optionals

现在我们知道了什么是可选方案以及我们为什么要使用它们，让我们看看 Swift 是如何使用可选方案的。

### 强制展开

将可选的视为类型的包装。我们必须把类型从可选的中解开才能使用它。例如，从可选邮件中打开邮件。强制展开意味着在不检查`nil`的情况下检索值。将`!`符号添加到一个值的末尾会强制打开它。

在上面的`checkMail`函数中，`mailBox.mail!`将可选邮件类型强制解包为邮件类型。然后可以使用`from`字段。由于`from`字段不是可选的，所以它不能是`nil`。因此，`trimmingCharacters`功能可以放心使用。

强制展开可能是危险的，因为它不检查`nil`值。上面的`checkMail`函数在使用`mail`属性之前执行一个`nil`检查(`mailBox.mail != nil`)。所以，强行打开它是安全的。然而，如果代码没有，并且`mail`是`nil`，则`mailBox.mail!.from`代码将导致崩溃。

### 隐式展开选项

在某些情况下，可以肯定的是，在初始化之后，一个 optional 永远不会是`nil`。每次都必须检查和打开这个值可能很麻烦。隐式打开这样一个可选的使它更容易使用。在类型后放置一个`!`会创建一个隐式展开的可选变量。它的形式是:

```
 let name: Type! 
```

Enter fullscreen mode Exit fullscreen mode

在下面的例子中，`myMailBox`有一个邮件项目。将`myMailImplicitlyUnwrapped`分配给`myMailBox.mail`使得`myMailImplicitlyUnwrapped`成为可选的。但是，我们知道它永远不会是`nil`。每次都要打开它会很烦人。将`myMailImplicitlyUnwrapped`声明为隐式展开的可选值会使它看起来像一个非可选值。虽然，还是一个可有可无的幕后。不需要`!`或`?`来访问整个代码中的值。

```
let myMailBox = MailBox()
myMailBox.mail = Mail(to: "Alice", from: "Bob")

let myMailImplicitlyUnwrapped: Mail! = myMailBox.mail

print(myMailImplicitlyUnwrapped.to) // the ! is not needed
print(myMailImplicitlyUnwrapped.from) 
```

Enter fullscreen mode Exit fullscreen mode

相比之下，下面的例子在每次使用`myMailOptional`时都使用`!`。

```
let myMailBox = MailBox()
myMailBox.mail = Mail(to: "Alice", from: "Bob")

let myMailOptional: Mail? = myMailBox.mail

print(myMailOptional!.from) // The ! is needed each time the value is used.
print(myMailOptional!.to) 
```

Enter fullscreen mode Exit fullscreen mode

### 使用的危险性！

使用`!`有风险。如果可选的是`nil`，使用它的值会导致程序崩溃！
然而，有一些使用`!`的用例:

*   便利
*   使用 Objective-C 平台时的兼容性
*   快速失败。例如，如果资源丢失，那么立即失败而不是继续。

最佳实践是尽可能避免它们。

### 可选绑定

可选绑定是使用`nil`检查的替代方法。它检查可选参数是否有非零值，并将该值赋给一个临时常量或变量。该临时常量/变量的范围在 if/while 块内。可选绑定的形式为:

```
if let temp = optional {
    //temp is only available within this code block
    //code to execute
}
//temp is not available here. 
```

Enter fullscreen mode Exit fullscreen mode

下面，`checkMail`函数被重写为使用可选绑定:

```
import Foundation
func checkMail(mailBox: MailBox) {
    if let mail = mailBox.mail {
        print("Mail from: \(mail.from.trimmingCharacters(in: .whitespacesAndNewlines))")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果`mailBox.mail`值不是`nil`，那么它的值被分配给临时`mail`常数。常数的作用域仅在 if 块中可用。if 块中不需要`!`，因为邮件的类型是邮件，而不是可选邮件。如果`mailBox.mail`值为`nil`，则不执行 If 块。

### 可选链接

可选链接允许访问其他结构中的选项。它提供了一种安全的方法来访问可能包含`nil`值的选项。要使用可选绑定，请在值后放置一个`?`符号。这就像在强制展开一个值时使用`!`符号一样。但是，可选的链接不会因为一个`nil`值而使程序崩溃。

再看一下`checkMail`函数，它可以使用可选的链接:
来重写

```
import Foundation
func checkMail(mailBox: MailBox) {
    if let from = mailBox.mail?.from {
        print("Mail from: \(from.trimmingCharacters(in: .whitespacesAndNewlines))")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我修改了可选的绑定部分，从`Mail`类中检索`from`属性。可选链接提供了访问可选邮件类型的安全方式。如果`mail`存在，那么 Swift 将获得`from`属性的值。由于`mail`可能是`nil`，那么访问`mailBox.mail?.from`的结果将返回一个`String?`而不是一个`String`。然后使用可选绑定来获取`from`值。

可选链接将使非可选值成为可选值。但是，该过程不会再次包装可选件。比如一个`String?`不会通过可选链接变成一个`String??`。

我修改了`Mail`类，以包含一个类型为`Float?`的`stamp`属性。(垃圾邮件传单没有邮票)。在这个例子中，我对 stamp 值进行了四舍五入(出于一些人为的原因):

```
class Mail {
    let to: String
    let from: String
    var stamp: Float?

    init(to: String, from: String) {
        self.to = to
        self.from = from
    }

}

let mailBox = MailBox()
mailBox.mail = Mail(to: "Alice", from: "Bob")

let rounded = mailBox.mail?.stamp?.rounded() 
```

Enter fullscreen mode Exit fullscreen mode

关于此代码的一些注释:

*   对`stamp`财产的访问是由`mailBox.mail?.stamp?.rounded()`而不是`mailBox.mail?.stamp??.rounded()`进行的
*   `rounded()`函数返回一个浮点数。但是，由于可选的链接，返回值将是`Float?`。所以，`rounded`常量是可选的！

我所有的例子都使用了属性。但是，可选链接也适用于方法和下标。

### 零聚结

Nil 合并展开一个可选的并返回它的值或者在`nil`的情况下返回一个默认值。零合并符号为`??`，其形式为:

```
optional ?? defaultValue 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个使用 nil 合并`stamp`属性的例子。

```
let stampAmount = mailBox.mail?.stamp ?? 0.0 
```

Enter fullscreen mode Exit fullscreen mode

这里，`stampAmount`或者是`stamp`的值，或者是默认值 0.0。它的类型是`Float`。

### 地图和平面地图

可选类型实现`map`和`flatmap`功能。这些功能的一个用途是作为`nil`检查的替代。

例如，假设我们有一个`openMail`函数。这个函数接受一个`Mail`参数，并将它的内容作为一个`String`返回。注意`Mail`参数是非可选类型。对于这个例子，我已经硬编码了返回一个`String`的函数。请想象一下，一个真正的实现实际上会做些什么。

```
func openMail(_ mail: Mail) -> String {
    //do processing
    return "mail contents"
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使用这个函数，我们可以编写如下代码:

```
if let mail = mailBox.mail {
    let content = openMail(mail)
    //do stuff with content
    print(content)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，可选绑定获取邮件属性的非零值。该值随后被传递给`openMail`函数。可以使用`map`函数重写这段代码。

map 函数允许我们将可选参数与带有非可选参数的函数一起使用。Map 展开可选的，如果值不是`nil`，则将该值传递给函数。示例:

```
if let mailContent = mailBox.mail.map(openMail) {
    //do stuff with mailContent
    print(mailContent)
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码更简洁一些。但是`nil`检查出了什么问题呢？好吧，假设我们改变了`openMail`函数来返回一个`String?` :

```
func openMail(_ mail: Mail) -> String? {
    //do processing
    let contents: String? = "mail contents"
    return contents
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以编写如下代码。

```
if let mail = mailBox.mail {
    if let openMail = openMail(mail) {
        print(openMail)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在代码已经嵌套了 if 块。这种结构可能很难维护，而且容易出错。用 flatMap 重写消除了嵌套的 if 语句。

```
if let mailContent = mailBox.mail.flatMap(openMail) {
    print(mailContent)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 可选上更多类型

在后台，可选类型是一个具有两个值的枚举，`none`和`some<Wrapped>`。

Swift 是开源的，所以我们可以看看 [GitHub](https://github.com/) 上的代码。以下摘自 [Optional.swift](https://github.com/apple/swift/blob/28b471c135af9401942697928702a5adb1b26960/stdlib/public/core/Optional.swift#L122-L134) 文件:

```
public enum Optional<Wrapped> : ExpressibleByNilLiteral {
  // The compiler has special knowledge of Optional<Wrapped>, including the fact
  // that it is an `enum` with cases named `none` and `some`.

  /// The absence of a value.
  ///
  /// In code, the absence of a value is typically written using the `nil`
  /// literal rather than the explicit `.none` enumeration case.
  case none

  /// The presence of a value, stored as `Wrapped`.
  case some(Wrapped) 
```

Enter fullscreen mode Exit fullscreen mode

可以使用可选的枚举形式。但是，这是一种冗长的编码风格，如下所示:

```
let number: Optional<Int> = .some(5)
let empty: Optional<Int> = .none

switch number {
case .some(let n):
    //n is of type Int, it is the unwrapped value of the number optional
    var z = n * 7
case .none:
    print("none")
} 
```

Enter fullscreen mode Exit fullscreen mode

`?`操作符是访问选项的语法糖。以下等价:

```
let number: Optional<Int> = .some(5)
let empty: Optional<Int> = .none

let sugarNumber: Int? = 5
let sugarEmpty: Int? 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

总而言之:

*   选项通过消除空指针类型错误为程序员提供了安全性。
*   Swift 提供了安全和不安全的方式来访问可选值。
    *   可选绑定提供了一种安全方便的方法来处理可选的。
    *   可选链接使用`?`操作符安全地打开可选链接。
    *   强制展开使用`!`操作符。它不提供任何安全性，因为`nil`值会导致程序崩溃。
    *   隐式取消包装的可选类型是可选的，但像非可选类型一样使用。这是一种方便的样式，当值在初始化后不再是`nil`时使用。
*   Nil 合并展开一个可选的并返回它的值或者在`nil`的情况下返回一个默认值。
*   `map`和`flatmap`函数提供了一种转换可选值的方法。
*   在幕后，可选的是具有两种情况`none`和`some<Wrapped>`的枚举。

感谢您的阅读！如果这篇文章有什么错误，请告诉我，因为我还在学习 Swift。

## 参考文献

*   [Swift 语言指南:基础:选项](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309)
*   [Swift 语言参考:可选类型](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Types.html#//apple_ref/swift/grammar/optional-type)
*   [苹果文档:可选](https://developer.apple.com/documentation/swift/optional)
*   [掌握 Swift 4 -第四版](https://www.packtpub.com/application-development/mastering-swift-4-fourth-edition)
*   [理解 Swift 选项的完整指南](http://matteomanferdini.com/complete-guide-to-swift-optionals/)
*   [运行中的 Java 8](https://www.manning.com/books/java-8-in-action)
*   [Swift 中的总编程量](https://medium.com/@andre_videla/total-programming-in-swift-526508c12a74)
*   [GitHub: Optional.swift](https://github.com/apple/swift/blob/master/stdlib/public/core/Optional.swift)

*这篇文章最初发表在我的[博客](https://monicagranbois.com/blog/swift/optionals/)T3 上*