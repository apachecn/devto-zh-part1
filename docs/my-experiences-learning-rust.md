# 我学习生锈的经历

> 原文：<https://dev.to/samipietikainen/my-experiences-learning-rust>

最初发布在我的个人博客上。

在看到很多关于 Rust 语言的积极讨论后，我决定自己也研究一下。所以，在过去的几周里，我一直在阅读 Rust 编程语言第二版的书籍，这是该语言的入门书籍。这篇博文总结了我对这门语言及其特性的第一印象，也是对其他感兴趣的人的一个快速介绍。在我的日常工作中，我主要使用 C 和 C++，所以这些是我学习一门新语言的主要参考点。

## 概述

在这本书的第一章中，Rust 的主要思想被创造如下。

> Rust 是一种专注于安全性、速度和并发性的编程语言。它的设计允许您创建具有低级语言的性能和控制，但具有高级语言的强大抽象的程序。

来自嵌入式领域的我特别喜欢 Rust 对安全性、性能和并发性的高度关注。许多其他现代语言选择了垃圾收集的方法，许多语言还需要运行时，这使它们不适合低级编程。这大概也是 C、C++等语言仍然被非常广泛使用的主要原因之一。

Rust 是一种编译语言，二进制文件被直接编译成目标架构。Rust 还在编译期间进行安全检查，因此增加的安全性不会影响运行时性能，这使得它非常独特。

## 借用检查器

所有权是 Rust 中的一个关键概念。每个值都有一个变量是该数据的所有者，一次只能有一个所有者。如果值被移动到一个新的变量，所有权被转移，旧的变量变得无效。也可以使用引用来借用值，但是一次只能有一个可变引用。这些规则在编译时由借用检查器强制执行。

相比之下，C/C++没有类似于 Rust 的所有权概念。例如，在 C++中，可以同时有多个指针和对相同数据的引用，程序员有责任确保没有数据竞争。起初，它可能会觉得借用检查器“只是碍事”，因为它会产生许多编译器错误(至少在您习惯所有权概念之前)，但最终它提供了针对许多常见问题的有价值的安全防护。

## 没有例外

熟悉 C++、Python 和 Java 等语言的开发人员也应该熟悉异常作为一种错误处理机制。当发生错误时，将引发异常，调用堆栈将展开，直到捕获到异常。如果根本没有捕获到异常，程序就会终止。这种方案允许代码的某些部分，例如库，轻松地让应用程序逻辑以有意义的方式处理错误。然而，一个缺点是，尤其是在 C++中，很难判断哪些异常会被抛出，而且错误处理代码可能远离触发错误的部分。

Rust 中的错误处理方法是不使用异常。相反，Rust 有两种不同的错误处理机制。如果错误不可恢复，程序将会死机(即终止)。或者，对于可恢复的错误，使用类型`Result<T,E>`。使用这种方法，编译器可以强制在从`Result`获得返回值时总是处理错误变量。这也比将失败信息编码为返回值(例如返回-1)更健壮。此外，Rust 还提供了使用`?`操作符从函数中轻松传播错误的语法。

```
fn file_len() -> Result<u64, io::Error> {
    Ok(File::open("test.txt")?.metadata()?.len())
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，如果 **open** 或 **metadata** 调用失败，返回的错误会自动传播，而不需要显式读取并返回它。这删除了不必要的样板代码，使结果代码更简单、更整洁。

## 列举类固醇

我有很强的 C/C++背景，所以对我来说，枚举只是一个使用整型作为底层类型的显式命名常量的集合。在 Rust 中，枚举远不止这些，它们更好地被描述为代表一个或多个变体的类型。这些变体也可以包含数据，而且每个变体也可以有不同的数据。

这使得枚举类型不仅适合枚举可能的变量，而且适合将它们的数据建模为相同的类型。例子可以从 [Rust by example](https://rustbyexample.com/custom_types/enum.html) 网站上找到。

## 说好了！

Rust 的另一个很酷的特性是模式。模式和模式匹配被用在很多地方，但可能最强大的是`match`操作符。可以认为是，嗯，`switch-case`打了类固醇。除了匹配简单的值，还可以有复杂的模式和额外的匹配保护。此外，编译器强制所有枚举变量总是被处理。这里有一个例子:

```
let p = (4,3,2,1);

match p {
    (2, ..)             => println!("first is 2"),
    (a, .., b) if a > 2 => println!("first {}, last {}", a, b),
    _                   => println!("default...")
} 
```

Enter fullscreen mode Exit fullscreen mode

如果元组中的第一个字段是 2，则第一种情况(Rust 中称为匹配臂)匹配。第二个分支捕获第一个和最后一个字段(以便它们可以在匹配体中使用),如果第一个字段大于 2，则进行匹配。最后，第三个分支有一个通配符，将匹配所有剩余的情况。这也满足了需要处理所有案例的要求。这只是一个简单的例子，Rust book 中有一整章[专门讨论模式。基本上，非常灵活的枚举结合模式匹配提供了一种非常有表现力的建模和操作数据的方式。](https://doc.rust-lang.org/book/second-edition/ch18-00-patterns.html)

## 式推理和胁迫

Rust 是一种静态类型语言，这意味着编译器在编译期间必须知道所有的类型。在像 C 这样的其他静态类型语言中，这意味着程序员必须明确地告诉变量、参数和返回值是什么类型。在 Rust 中，一种叫做类型推断的技术被用来推断类型。因此，即使语言是静态类型的，大多数时候程序员也不需要显式地编写类型。编译器能够从上下文中找出它。

```
// create a empty vector without annotating the type
let mut a = Vec::new();
// here the compiler is able to see that it is Vec<String>
a.push(String::from("test")); 
```

Enter fullscreen mode Exit fullscreen mode

Rust 还使用 deref 强制，这个简单的例子很容易演示。

```
use std::sync::Arc;

fn func(s: &String) {
    println!("{}", s);
}

fn main() {
    let s = Arc::new(String::from("test"));
    /* access value in Arc with * and then take reference with & */
    func(&*s);
    /* Compiler does deref coercion and translates &s automatically to &*s */
    func(&s); 
} 
```

Enter fullscreen mode Exit fullscreen mode

首先创建原子引用计数的字符串对象(用[弧](https://doc.rust-lang.org/std/sync/struct.Arc.html)包裹的字符串)。然后用这个对象调用一个引用 string 的函数。首先用`*`操作符显式访问`Arc`中的对象。Deref 强制允许我们只键入& s，因为很明显我们打算使用`Arc`中的值。总的来说，在编译器无需显式注释就能找出正确类型的情况下，强制和推理允许简化代码。现代 C++有`auto`关键字，但是 Rust 把这个概念更进一步。

## 零成本抽象

Rust 的卖点之一是零成本的抽象。实际上，这意味着能够使用高级概念，如闭包和迭代器适配器，而不会有任何性能损失。此外，当使用这些高级方面时，生成的代码与手工编码实现一样快，或者比手工编码实现更快。

下面这个例子展示了 Rust 中的函数式编程:

```
use std::collections::HashMap;

fn main()
{
    let mut dict = HashMap::<char, usize>::new();

    String::from("test string").chars().for_each(|x| {
        let item = dict.entry(x).or_insert(0);
        *item = *item + 1;
    });

    println!("{:?}", dict);
} 
```

Enter fullscreen mode Exit fullscreen mode

该代码计算输入字符串中字符的出现次数，并生成带有字符计数对的`HashMap`。上面的例子输出:

```
{'t': 3, 'e': 1, 's': 2, ' ': 1, 'r': 1, 'i': 1, 'g': 1, 'n': 1} 
```

Enter fullscreen mode Exit fullscreen mode

## 没有空值

Rust 中一个有趣的想法是该语言没有 null 的概念。乍一看，这似乎很奇怪，因为我们都习惯了，但它确实完全消除了无数常见的错误。在 C/C++等其他语言中，必须显式检查指针是否为空，否则如果空对象被取消引用，应用程序将崩溃。

通常空值用来表示某个资源无效，这个信息由一个特殊的空值来表示。问题是 null 条件需要手动检查，这很容易出错。Rust 中仍然需要不一定有效的概念。只是实现方式不同而已。

Rust 不是通过特殊值来指示对象的无效状态，而是通过将实际对象包装在`Option<T>`或`Result<T,E>`类型中来实现这一点。这样，当访问具体类型时，编译器将强制处理失败情况，否则代码将无法编译。

## 无畏并发

其中一个大目标，也是 Rust Book 的[章节](https://doc.rust-lang.org/book/second-edition/ch16-00-concurrency.html)之一，是无畏并发。正如所料，Rust 提供了我们熟悉的原语，如线程、互斥体以及消息传递通道。然而，生存期模型和所有权规则使得使用并发性比在许多其他语言中更安全。

例如，Rust 能够检测线程间的数据竞争。也就是说，当多个线程试图在没有适当互斥的情况下修改相同的数据时。所有权系统和互斥体的实现方式也保证了锁总是被获取和释放。trait 系统还用于“标记”线程安全的类型，这意味着如果在线程上下文中使用不安全的方法，程序将无法编译。

## 约定优于配置

Rust 使用了一种超越配置设计范式的约定，旨在减少开发人员需要做出的决策数量。这种范例在代码和测试组织中被大量使用。代码模块、源文件、单元测试和集成测试是以某种方式组织的，编译器能够在没有显式配置的情况下找到这些项目。

除了减少配置之外，这种范例的主要好处之一是不同的项目以相似的方式组织，这使得导航其他项目的源代码更加容易。C++肯定不是这种情况。

## 有用的编译器错误信息

如果你曾经在 C++模板(例如 STL)上犯过错误，你可能知道什么有用的错误信息看起来不像。你很可能会得到几个屏幕的无法理解的文本。即使对于最简单的错误，错误消息也可能是含糊不清的。

到目前为止，我对 Rust 的经验是，编译器错误真的很有帮助，通常会指出确切的错误，甚至提出修复建议。例如，如果前面的`file_len`例子在`Ok(File::open(“test.txt”)?.metadata()?.len());`行中有一个额外的`;`，编译器错误是:

```
error[E0308]: mismatched types
 --> src/main.rs:5:41
  |
5 |   fn file_len() -> Result<u64, io::Error> {
  |  _________________________________________^
6 | |     Ok(File::open("test.txt")?.metadata()?.len());
  | |                                                  - help: consider removing this semicolon
7 | | }
  | |_^ expected enum `std::result::Result`, found ()
  |
  = note: expected type `std::result::Result<u64, std::io::Error>`
             found type `() 
```

Enter fullscreen mode Exit fullscreen mode

*“考虑去掉这个分号”*甚至在代码中也有指出。相当有用。这很重要，因为特别是在开始的时候，由于 Rust 做了很多检查，你很可能经常出错(至少我是这样)。这当然是一件好事，因为检查指出了代码中潜在的错误。但是当编译器给你指出正确的方向时，它肯定会有很大的帮助。

## 严格的类型和类型转换

在 C 和 C++中，编译器自动转换数值类型。因此，C/C++开发人员会认为将 uint8 分配给 uint32 应该就可以了。

```
let a: i8 = 5;
let b: i32 = a; 
```

Enter fullscreen mode Exit fullscreen mode

Rust 对类型的要求更严格，上面的代码会产生错误。程序员必须显式地将值转换为正确的类型`let b: i32 = a as i32;`。

## 是否面向对象？

继承使 Rust 有别于许多常见的高级语言。这是因为 Rust 没有这个特性，尽管它有其他面向对象的特性，比如封装。另一方面，Rust 有一个称为 traits 的特性，用来描述一种常见的行为，就像在其他语言中使用接口一样。此外，trait 对象可以像继承语言中的多态类型一样使用。

Rust 是否被归类为面向对象取决于定义。面向对象的模式仍然可以在 Rust 中实现，但是这种方法可能需要与“教科书”中的实现稍有不同。锈书里有完整的[章](https://doc.rust-lang.org/book/second-edition/ch17-00-oop.html)专门讨论这个。

## 结论

关于铁锈还有很多可以写的。例如，支持[单元和集成测试](https://doc.rust-lang.org/book/second-edition/ch11-00-testing.html)或[货物工具和板条箱. io](https://doc.rust-lang.org/book/second-edition/ch14-00-more-about-cargo.html) 。不过，这里的主要思想是简要介绍一些我觉得特别有趣的关于语言和标准库的方面。如果这让你感兴趣，我真的会推荐[锈书](https://doc.rust-lang.org/book/second-edition/ch01-00-introduction.html)。它很容易理解，涵盖了所有的语言，最重要的是完全免费。

据我目前所知，Rust 似乎解决了编程中的许多常见挑战。最重要的是，这些额外的检查和保证是在编译期间完成的，因此它们没有运行时损失。该语言还被直接编译成机器码，这使得它适合嵌入式目标(垃圾收集或运行时不适合)。作为我的第一个项目，我开始在自动序列化和反序列化的 REST 客户端上工作。总的来说，我的第一印象是用这种语言做了很多正确的事情。迫不及待地想潜入更深的地方！