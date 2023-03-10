# 榆树和铁锈教给我们关于未来的什么

> 原文：<https://dev.to/martinmodrak/what-elm-and-rust-teach-us-about-the-future>

所以我最近开始用 Elm 编程，也用 Rust 做了一些东西。老实说，这主要是一个炒作驱动的决定，但旅程绝对是值得的。我还注意到，尽管这两种语言的目标受众和用例不同，但它们做出了许多相似的设计决策。我认为这不是巧合。很有可能十年后，Elm 和 Rust 都将被遗忘，但我确信它们所基于的思想将会在我们那时使用的语言中存在。这是一篇关于我在《榆树和铁锈》中发现的迷人想法的帖子。

首先快速声明:我不是这两种语言的专家，虽然我开始对 Elm 感到舒适，但毫无疑问，我是一个新手，所以如果我对任何一种语言不公平，请纠正我。

# 设定场景

Rust 是一种旨在与 C++竞争的系统语言。Rust 重视性能、并发性和内存安全，但不进行垃圾收集。Rust 编译为本机二进制文件，不仅适用于主流 x86/x64 平台，也适用于 ARM 甚至某些基于 ARM 的微控制器。

Elm 是一种与 Javascript 竞争的 web 应用程序语言，尤其是虚拟 DOM 框架(如 ReactJS)。Elm 编译成 Javascript，垃圾收集，纯功能性。Elm 重视简单性和可靠性。

这两种语言都已经可以用于实际项目，但是生态系统仍然不成熟，语言本身仍然在发展。

虽然我喜欢这两种语言，但我不打算将这篇文章局限于积极的方面，也将提到(对我来说)什么是痛点。我将从这两种语言的共同之处开始，稍后会给出关于这两种语言的更多细节。

# 常见主题

这里描述的特性大部分都不是全新的，可以在 OCaml、Haskell 和 F#等语言中找到。有趣的是，Elm 和 Rust 证明了它们对于各种各样的用例都是有用的。

## 标记工会

这是一个很小但非常实用的特性——我想说标记的联合是类固醇上的枚举。想想看，你多久写一次这样的东西:

```
enum AccountType {Savings, CreditCard};     

//In real code please use Decimal types to represent money. Please.
class CreditParams {
    int creditLimit; 
    ...
}

class Account {
    AccountType accountType;
    int balance;
    CreditParams creditParams; //only present for CreditCard, always null for Savings       
} 
```

这为一些可爱的错误留出了空间，因为您的数据模型可以表示一种不应该出现的状态(信用参数为非空的储蓄帐户，或者信用参数为空的信用卡帐户)。程序员需要注意的是，对`Account`对象的任何操作都不会导致这样一种状态，这种状态可能很重要并且容易出错。这也造成了不确定性，例如，有多种方法可以获得一个账户的信用额度:

```
//Yes I know, this should be a class method
int getCreditLimit1(Account account) {
    if (account.creditParams != null) { 
        //wrong if account.accountType == Savings
        return account.creditParams.creditLimit;
    } else {
        return 0;
    }
}

int getCreditLimit2(Account account) {
    if (account.accountType == CreditCard) { 
        //possibly accessing a null pointer
        return account.creditParams.creditLimit; 
    } else {
        return 0;
    }
} 
```

一个更可取的选择是[让不可能的状态变得不可能](https://medium.com/elm-shorts/how-to-make-impossible-states-impossible-c12a07e907b5#.rbysmekjt)。标记联合允许您通过将异构数据附加到每个变量来实现这一点。这让我们将数据模型重写为(Rust 语法，[在线试用](https://is.gd/GSoO8n)):

```
 struct CreditParams {
    credit_limit: i32, //i32 is a 32bit signed int
    ...
} 

enum AccountDetails {
    Savings, //Savings has no attached data
    CreditCard(CreditParams), //CreditCard has a single CreditParams instance
}

struct Account { 
    balance: i32, 
    details: AccountDetails,
  } 
```

对于带标记的联合，如果不显式检查类型，就无法访问附加的数据——所以只有一种方法可以获得信用限额，而且总是正确的(Rust 语法，[在线尝试](https://is.gd/GSoO8n)):

```
fn get_credit_limit(account: Account) -> i32 {
    match account.details { //match is like case
        AccountDetails::CreditCard(params) =>  //bind local variable params to the attached data
            params.credit_limit,    //in Rust, return is implicit
        AccountDetails::Savings => 
            0
    }
} 
```

因为 Elm 和 Rust 都没有空值，所以在构建`AccountDetails`实例时必须指定`CreditParams`，所以上面的代码在任何情况下都是安全的。

另一个好处是，在 Elm 和 Rust 中，您必须处理所有可能的标记联合情况(或者提供一个默认分支)。未能处理所有情况是一个编译时错误。通过这种方式，编译器确保您在扩展`AcountDetails`时更新了所有代码。

## 式推断

有些人喜欢静态类型，因为用静态类型的语言编写错误的代码更难。有些人喜欢动态类型，因为它避免了为所有东西添加类型注释的官僚作风。类型推断试图两全其美:语言是静态类型化的，但是您很少需要提供类型注释。Rust 和 Elm 中的类型推理有点像 C++中的`auto`,但是它更强大——它着眼于更广泛的上下文，并且还考虑了下游代码。比如说(Rust 语法，[在线试试](https://is.gd/izOGxZ))

```
// The compiler infers that elem is a float.
let elem = 1.36;

//Explicit type annotation - f64 is a double precision float
let elem2: f64 = 3.141592;

// Create an empty vector (a growable array).
let mut vec = Vec::new();
// At this point the compiler doesn't know the exact type of `vec`, it
// just knows that it's a vector of something (`Vec<_>`).

// Insert `elem` and `elem2` in the vector.
vec.push(elem);
vec.push(elem2);
// Aha! Now the compiler knows that `vec` is a vector of doubles (`Vec<f64>`)

//The compiler infers that s is a &str (reference to string)
let s = "Hello";

//Compile-time error: expected floating-point variable, found &str
vec.push(s); 
```

Rust 中的类型推断有一定的局限性，所以现在仍然需要显式的类型注释。但是 Elm 走得更远，实现了一个 [Hindley-Milner 类型系统](https://en.wikipedia.org/wiki/Hindley%E2%80%93Milner_type_system)的变体。实际上，这意味着 Elm 中的类型注释基本上只是注释(除了一些[怪异的极端情况](https://github.com/elm-lang/elm-compiler/issues/1353))。虽然 Elm 编译器强制类型注释与代码匹配，但是它们可以被省略，编译器仍然会静态地对所有内容进行类型检查。然而，不要用类型注释函数是一个警告，因为类型注释会让编译器给出更好的错误消息，并迫使你清楚地表达你的意图。

## 永恒性

不变性意味着变量/数据在初始赋值/创建后不能被修改。另一种说法是，对不可变数据的操作除了返回值之外，没有任何可观察到的效果。这意味着不可变数据上的函数将总是为相同的参数返回相同的值。处理不可变数据的代码更容易理解和推理，并且本质上是线程安全的。考虑具有可变数据的代码:

```
address = new Address();
address.street = "Mullholland Drive";
...
person = new Person();
person.primaryAddress = address;
print(person.primaryAddress.street) //Mullholland Drive
...
address.street = "Park Avenue"
...
print(person.primaryAddress.street) //Park Avenue 
```

现在假设我们想弄清楚`person.primaryAddress.street`为什么会改变。由于数据是可变的，找到`person.primaryAddress`的所有用法是不够的——我们还需要检查分配给`person.primaryAddress`的所有变量/字段的整个树。有了不可变的数据结构，这个问题就可以避免了，因为程序员不得不写类似这样的代码:

```
address = new Address("Mullholland Drive", 1035, "California");
//Elm and Rust also support syntax of the form:
//address = { street = "Mullholland Drive", number = 1035, state = "California" }
...
person = new Person(address);
...
address.street = "Park Avenue" //not allowed, the object is immutable 
```

关于为什么不可变性是好的更详细的讨论，例如参见 [3 使用不可变对象的好处](https://dev.to/0x13a/3-benefits-of-using-immutable-objects)。

Elm 在不变性上孤注一掷——一切都是不可变的，任何函数都不会有副作用。Rust 稍微宽松一点:在 Rust 中，您必须选择加入可变性，编译器确保只要一段数据可以在一个代码段内更改(有一个对该数据的可变引用)，就没有其他代码路径可以读取或修改相同的数据。

### 永恒性的问题

确保您引用的数据在没有您的合作下不会更改，通常会让您的生活更轻松。除非这正是你想要达到的目的。假设您正在编写一个流量监控工具。您可能希望像这样对数据建模(Elm 语法):

```
-- In Elm, double dash marks a comment
type alias City =         --Curly braces declare a record type, a bit like an object
  { name: String
  , routes: List Route    --list of Route instances
  }

type alias Route =
  { from: City
  , to: City
  , trafficLevel: Float
  }

type alias World =
  { cities: List City
  , routes: List Route
  } 
```

您可能希望当您收到新的交通信息时，您只需使用`World.routes`即可，通过`City.routes`访问就会看到变化。但你可能错了。在 Elm 中，这甚至不会被编译(记录类型中的字段在编译时是完全展开的，因此不能有循环引用)。如果您使用标记的联合来编译模型，通过`World.routes`访问的`trafficLevel`可能与通过`City.routes`访问的不一样，因为它们总是表现为不同的实例。

Rust 中类似的数据模型可以编译，但是很难实际实例化该结构，并且您将无法修改任何`Route`实例的`trafficLevel`，因为编译器不允许您创建对它的可变引用(每个`Route`至少被引用两次)。

这给我们带来了一个很少被提及的不变性的含义:**不可变的数据结构本质上是树状的**。在 Elm 和 Rust 中，使用类似图形的结构是一件痛苦的事情，你必须放弃语言给你的一些保证。

在 Elm 中，表示图的唯一方式是使用字典(map)的索引，而不是直接引用。对于上面的例子，一个实际的数据模型可能是这样的:

```
type alias RouteId = Int    -- New types just for clarity
type alias CityId = Int 

type alias City =
  { id: CityId 
  , name: String
  , routes: List RouteId 
  }

type alias Route =
  { id: RouteId
  , from: City
  , to: City
  , trafficLevel: Float
  }

type alias World =
  { cities: Dict CityId City     --dictionary (map) with CityId as keys and City as values
  , routes: Dict RouteId Route
  } 
```

请注意，没有什么可以阻止我们将无效的`RouteId`存储在`City.routes`中。虽然 Elm 为您提供了处理这种模型的好工具(例如，它迫使您总是处理给定的`RouteId`在`World.routes`中不存在的情况)，并且其他用例的优势使这成为一个可接受的成本，但它仍然有点烦人。

Rust 有更多的选项来处理类似图形的数据，但它们都有自己的缺点([使用索引](http://smallcultfollowing.com/babysteps/blog/2015/04/06/modeling-graphs-in-rust-using-vector-indices/)， [StackOverflow 讨论](http://stackoverflow.com/questions/34747464/implement-graph-like-datastructure-in-rust)，[使用引用计数或竞技场分配的图形](https://github.com/nrc/r4cppp/blob/master/graphs/README.md))。

## 智能但限制性强的编译器

这基本上是对前面具体特征的概括。Elm 和 Rust 的编译器非常强大，它们为你做了很多事情。他们不仅一行一行地解析代码，而且在整个程序的上下文中推理你的代码。然而，Rust 和 Elm 编译器最有趣的地方不是它们让你做什么。这是他们不允许你做的事情(例如，没有显式转换，你不能混合浮点数和整型数；没有处理所有可能的情况，你不能访问存储在标记联合中的数据；你不能修改某些数据，等等)。).同时，编译器足够聪明，可以让遵守这些限制变得不那么麻烦。如果你认为给程序员更少的限制会产生更好的代码，想想人们抱怨限制 GOTO 的使用会阻碍生产力的时候。

表述这一立场的另一种方式是，语言不应该努力让最佳实践变得容易，而是应该让编写糟糕的代码变得困难。我认为这两种语言都很好地实现了这一点——编写*任何*代码都比它们限制较少的亲戚要难一点，但是走捷径的动机要少得多。

实际上，智能但限制性强的编译器意味着花在编码上的时间更多，花在调试上的时间更少。由于调试和阅读混乱的代码非常耗时，这通常会带来净生产率的提高。就我个人而言，我喜欢写代码，而调试往往令人沮丧，所以对我来说，这是一个甜蜜的交易。

不用说，所有这些限制都使得在 Rust 或 Elm 中破解一次性的脏解决方案有点烦人。但是什么代码是真正一次性的呢？

## 风格要紧

Elm 和 Rust 的社区都大力推动源代码的一致表示。至少，这减少了每个使用该语言的团队对冗长的特定于项目的风格指南的需求。具体来说，Elm 编译器对某些语言结构强制缩进，[不允许使用制表符来标识](https://github.com/elm-lang/elm-compiler/issues/1370)(!)并强制类型以大写字母开始，而函数以小写字母开始。此外，还有一个社区认可的源代码格式化程序 [elm-format](https://github.com/avh4/elm-format) 。

同样，如果您不遵守官方命名约定，Rust compiler 会给出警告，并提供一个社区认可的格式化程序 [rustfmt](https://github.com/rust-lang-nursery/rustfmt) 。

# 更多关于榆树

如果你仍然感兴趣的话，现在是单独谈论这些语言的时候了。我们先拿[榆树](http://elm-lang.org)。Elm 是一种简单的小型语言。完整的语法可以[记录在一页](http://elm-lang.org/docs/syntax)上。Elm 瞄准的是已经在用 Javascript 的人，争取低门槛进入。Elm 目前的版本是 0.18，新的版本经常带来向后不兼容的变化(尽管官方的转换工具是可用的)。有趣的是，在过去的几个版本中，删除的语法元素比添加的多，证明了对语言简单性的关注。

Elm 是纯功能性的。这意味着没有经典意义上的变量，一切都是函数。如果没有变量，应用程序如何随时间发展？这是由[榆树架构](https://guide.elm-lang.org/architecture/)(茶)处理的。在最简单的层面上，Elm 应用程序主要由一个`update`函数和一个`view`函数组成。`update`函数从用户/环境获取应用程序的先前状态和输入，并返回应用程序的新状态。然后，`view`函数获取应用程序的状态并返回一个 HTML 表示。因此，对应用程序状态的所有更改都发生在 Elm 代码之外，在 TEA 的本机代码之内。该架构还提供了必要的魔力来正确有效地更新 DOM 以匹配最新的`view`结果。

TEA 迫使你明确地说出什么构成了你的应用程序及其输入的状态。这让 Elm 提供了它的杀手级特性:[时间旅行调试器](http://elm-lang.org/blog/the-perfect-bug-report)。本质上，当调试器打开时，您可以重放应用程序的整个历史，并检查过去任何时间点的应用程序状态。由于这种语言的设计方式，它 100%的时间都是有效的。

TEA 的另一大好处是，您永远不必担心当用户单击复选框时忘记隐藏元素。如果您的`view`函数基于当前应用程序状态正确显示了元素，那么一旦应用程序状态再次改变，该元素也会自动隐藏。

Elm 的另一个好处是它努力让[有好的、有用的错误消息](http://elm-lang.org/blog/compiler-errors-for-humans)，并有一个专门的 GitHub 库供[建议错误消息改进](https://github.com/elm-lang/error-message-catalog/issues)。还有[记录系统](http://elm-lang.org/docs/records)，它在使用结构化类型时给你很大的自由度(例如，你不必在使用前声明它们)，但同时也静态地检查正确性。

## 痛点在榆树

TEA 的一个大缺点是它假设应用程序的所有状态都是显式的。这使得在特定的上下文中处理具有自己的状态的 HTML 元素变得棘手(例如，[文本区域内容](https://groups.google.com/d/topic/elm-discuss/ALKjx3bsCgc/discussion)，[文本区域中的插入符号位置](https://github.com/elm-lang/html/issues/55)，Web 组件)。你需要小心防止茶破坏这些成分。此外，TEA 可能是资源密集型的，尽管[不如可比较的 JS 框架](http://elm-lang.org/blog/blazing-fast-html-round-two)。最后但同样重要的是，在 Elm 中创建大型应用程序需要编写大量的样板代码。榆树社区[还在讨论](https://groups.google.com/d/topic/elm-discuss/FHmv9hBdSA0/discussion)如何开发[大型项目](https://groups.google.com/d/topic/elm-discuss/I1OBptGOU_A/discussion)更[轻松](https://groups.google.com/forum/#!searchin/elm-discuss/large%7Csort:relevance/elm-discuss/2RTddO_4rLw/xOmzeg6wAgAJ)。

# 关于铁锈的更多信息

> 哇，有很多新语法！
> 
> > 锈书，[第 4.34 节关于宏](https://doc.rust-lang.org/beta/book/macros.html)

与榆树相比，锈病更可怕。有很多语法和很多东西要学。然而这并不意外:如果你想写快速代码，你真的需要很多控制。此外，C，尤其是 C++也有大量的语法，所以 Rust 在这里绝对不是一个大的劣势。Rust 目前是 1.15 版本，有[向前兼容保证](https://blog.rust-lang.org/2014/10/30/Stability.html)。

虽然 Rust 是必不可少的，但它吸收了许多有用的函数式编程概念，并号称零成本抽象(T1)，也就是说，与手工调整但肮脏的解决方案相比，让你开发代码的所有花哨的语法技巧都不会带来实际的性能损失。

Rust 也没有通常的 OOP，相反它有[特征](https://doc.rust-lang.org/book/traits.html)(有点像接口)并且故意避免继承(你应该用 compose 代替)。

Rust 最奇怪也最有趣的部分是[借货检查器](https://doc.rust-lang.org/book/ownership.html)。虽然 Rust 没有托管内存(垃圾收集)，但它仍然可以保证您不能访问未初始化的内存、取消引用空指针或以其他方式损坏您的内存。这不仅对可靠性而且对安全性都有很大的影响，因为 Rust 会自动防止各种严重的攻击，如[缓冲区溢出](https://en.wikipedia.org/wiki/Buffer_overflow)或[心脏出血](https://en.wikipedia.org/wiki/Heartbleed) ( [博客文章](https://tonyarcieri.com/would-rust-have-prevented-heartbleed-another-look))。Rust 还可以防止大多数(但不是全部)内存泄漏。借用检查器通过验证您的程序在*编译时*正确访问内存，即没有托管内存的运行时损失，来实现这些保证的大部分。借用检查器确保对一段数据的可变引用不能与任何其他引用共存(因此，您不能在持有对它的引用时释放内存)。出于某种直觉，Rust 中的可变引用的行为有点像 C++中的`std::unique_ptr`([specs](http://en.cppreference.com/w/cpp/memory/unique_ptr))，但是在编译时强制了唯一性。更详细的描述不适合在这里，所以检查 [Rust by Example](http://rustbyexample.com/scope/borrow.html) 或只是谷歌了:-)。

## 痛点在铁锈上

借检查器既是 Rust 最大的长处，也是它最大的弱点。尽管 Rust 社区花了很大力气让大部分代码正常工作，但你最终还是不可避免地要与和[借款检查器](https://ayende.com/blog/176801/the-struggle-with-rust)对抗。对借用检查器的一些有希望的[更新正在进行中，可能会使 Rust 程序员的生活变得更容易，但这不会很快实现——让编译器理解你的程序是很难的(对程序员和编译器都是如此)。](http://smallcultfollowing.com/babysteps/blog/2016/04/27/non-lexical-lifetimes-introduction/)

虽然 Rust 非常重视性能，并且理论上编译器应该能够比 C/C++做更多的优化，但是 Rust 还没有做到。我见过的基准测试在 gcc 上将其等同于或略落后于 C/C++(例如[基准测试游戏](http://benchmarksgame.alioth.debian.org/u64q/which-programs-are-fastest.html))。在我的记忆中，gcc 产生的代码也比 MSVC 或英特尔编译器慢，这对 Rust 来说是个坏消息。然而，互联网表明，最近的 gcc 与 MSVC/英特尔不相上下，但我找不到任何好的基准链接。

Rust 中的开发仍然有一些粗糙的边缘， [IDE 支持是不完整的](https://areweideyet.com/) -建立一个像样的调试环境可能需要 [14 步过程](https://sherryummen.in/2016/09/02/debugging-rust-on-windows-using-visual-studio-code/)，并且功能仍然有限。

# 总结性的

正如函数式编程从边缘发展成为主流语言一样，我相信让 Elm 和 Rust 变得有趣的特性将会出现在主流中。
一些想法也可以立即转移到当前的语言中(例如[immutableejs](https://facebook.github.io/immutable-js/))。我认为这篇文章传达的信息是，你应该考虑学习一门新的语言。最好是一个与你目前为止所做的工作非常不同的工作。不，它不仅有趣，而且会让你在你选择的语言中成为一名更好的程序员。

如果你能在这里，在我的推特上或者在红迪网上提供你的反馈，我会非常高兴。