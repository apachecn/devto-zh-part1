# 一个 C#-er 与斯威夫特的恋情

> 原文：<https://dev.to/jonstodle/a-c-ers-love-affair-with-swift>

我选择的语言是 C#。这部分是因为它是我正确学习的第一门语言，部分是因为它熟悉和舒适，部分是因为我喜欢它的语法。

回到九月或十月，我读到有人建议每年学习一门新语言是一个好习惯。起初我认为这听起来有些过分，现在我认为这是一个很好的实践。这部分是因为我最终学习了 Swift。

我想开发一个原生的 iOS 应用程序，现在我不必使用 Objective-C 了。当然，我可以用 [Xamarin](https://www.xamarin.com/) 来做这件事，但我想尝试苹果的方式。

在花了几个月的时间熟悉 Swift 并学习 Swift 做事的方式后，有些东西我真的很喜欢。我想体验一下这些特征。有些在 C#中会很好，有些在 C#中没有意义(也没关系)。

在下文中，我将对不同的概念使用 C#名称，而不是 Swift 中的正确名称。

# 类扩展

这些在 Swift 中被大量使用，而且理由很充分；它们非常方便。它们有点类似于分部类，但是适用于所有的类，而不仅仅是你自己的类。我将在下面展示它们的不同之处。

Swift 中的类扩展允许您向现有类添加额外代码。在扩展中，您可以添加新的静态和实例方法、getter 属性和新的构造函数。你也可以让一个类实现一个接口(或者 Swift 中的协议)。

虽然我们可以用扩展方法在 C#中添加实例方法，但如果能够将一些方便的方法作为静态方法添加到一个类或一个构造函数的简化重载中，那就更好了。

类扩展不局限于你自己的类；你可以扩展任何类。这包括系统框架中的密封类或您添加的库中的类。你不能重写任何东西，但是重载成员是完全可能的。如果你遇到一个类，如果它实现了`IEnumerable<T>`使 Linq 可用，那就太好了，你可以实现它。

# 枚举速记

当我第一次开始学习 C#和 Silverlight 的时候，有一件事让我很困扰，那就是设置 UI 元素的可见性是这样做的:

```
MyControl.Visibility = Visibility.Visible; 
```

Enter fullscreen mode Exit fullscreen mode

“为什么我要写两遍可见性？”。虽然现在对我来说很明显(ðÿ˜›已经有一段时间了)，它是一个枚举，但我从来不喜欢它的冗长。

在 Swift 中，您可以通过省略枚举名称来引用枚举成员。上面的例子可以写成这样:

```
MyControl.Visibility = .Visible; 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，它不仅感觉自然，而且我认为它更具可读性。如果你想的话，你当然可以把整个事情写下来，但是当事情很明显的时候，你可以把它缩短。

在 C# 7 中，你可以用新的模式匹配:
做一些漂亮的事情

```
switch (MyControl.Visibility)  
 {
    case Visibility.Visible: return true;
     case Visibility.Hidden: return false;
     case Visibility.Collapsed: return false;
     default: false;
 } 
```

Enter fullscreen mode Exit fullscreen mode

但是如果有:
会更好

```
switch (MyControl.Visibility)  
{
    case .Visible: return true;
    case .Hidden: return false;
    case .Collapsed: return false;
    default: false;
} 
```

Enter fullscreen mode Exit fullscreen mode

大量的重复消失了，增加了可读性。

这个概念实际上很像 C# 6 中引入的`using static`。我认为在某种程度上，将枚举简写引入 C#是可能的。

# 可选试

Swift 处理异常(或 Swift 中称之为错误)的方式很有趣。每当你调用一个可能抛出错误的方法时，你必须在它前面加上一个`try`子句:

```
try aThrowingMethod(); 
```

Enter fullscreen mode Exit fullscreen mode

我很喜欢这样，因为它让你和其他可能阅读你的代码的人清楚地知道，什么时候你必须考虑可能的异常。不过，您选择如何处理潜在的异常是一个好的方面:

如果像上面那样放入一个`try`，就必须添加一个`catch`子句来处理潜在的异常。

您还可以告诉 Swift，您可以通过在`try`子句后追加一个`!`:
来保证该方法不会抛出异常

```
try! aThrowingMethod(); 
```

Enter fullscreen mode Exit fullscreen mode

如果抛出，将导致应用程序崩溃。这相当于在 C#中没有捕捉到异常。

第三种方式，也是我最喜欢的方式，是在`try`子句后追加一个`?`。每当你在 Swift 中的某个东西后面加上`?`时，你就表明它可能是空的。将它与一个`try`子句一起使用将导致结果值为 null，如果它抛出一个异常:

```
var thisWillBeNull = try? thisMethodWillThrow(); 
```

Enter fullscreen mode Exit fullscreen mode

我发现这是一种很好的简洁的方式来做`try/catch`。然后，您可以执行一个简单的空检查来查看操作是否成功。

# 显式参数名

这大概是我刚开始学 Swift 的时候觉得最烦的一点，但是在用 C#编程的时候已经错过了。这些在 Objective-C 中非常冗长，但在 Swift 3+中更好。下面是一个 C#方法的例子，以及我如何用 Swift
编写这个方法

```
// C#
public Person FindPersonById(int id) { }

// Swift
func findPerson(by id: Int) -> Person { } 
```

Enter fullscreen mode Exit fullscreen mode

对于 C# eyes 来说，Swift 版本看起来怪异而错误，但它是这样工作的:`findPerson`当然是方法的名字。`by`是方法使用者的参数名称。`id`是方法块中参数的名称。

当这个方法被调用时，它看起来像这样:

```
findPerson(by: 1234); 
```

Enter fullscreen mode Exit fullscreen mode

Swift 方法应该读起来像一个句子，当你有多个参数时这变得特别好:

```
// Declaration
func scheduleNotification(for person: Person, at date: Date = Date()) { } // No return value

// Consumption
scheduleNotification(for: myPerson, at: someDate); 
```

Enter fullscreen mode Exit fullscreen mode

这很容易看出所有参数的作用；无论是第一次调用方法时，还是稍后读取代码时。

如您所料，IDE 会帮助您确定每个参数的类型，因此知道应该传入什么并不困难。

# 一般来说

在这里的最后，我只想对 Swift 中我喜欢的一些东西进行评论，但不值得它们自己的标题。

我真的很喜欢 Swift 迫使你思考可能的空值。如果一个变量没有用附加的`?`标记为可选的(比如:`nullableValue?`)，你根本不能给它赋值 null。你也不需要检查这些值是否为空。基元类型和引用类型都是如此。

正如我提到的，Swift 还强迫你在使用抛出方法时使用一个`try`子句，让你知道什么时候你必须考虑可能的异常。它还迫使您将可能抛出异常的方法标记为`throwing`。

Swift 也有可能无法实例化新实例的构造函数。这些被特别标记，并在失败时返回 null。你仍然可以让构造函数失败或者崩溃，但是如果构造函数失败的可能性合理的话，也有一个不错的方法。

# 关闭思绪

当我开始喜欢 Swift 的时候我很惊讶，当我回到 C#时错过了一些东西，我就更惊讶了。我认为这只是表明，在你偏爱的语言之外，还有一些真正好的想法。

快乐编码。

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/a-c-ers-love-affair-with-swift/)T3】*