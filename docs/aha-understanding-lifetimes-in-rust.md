# 啊哈！了解铁锈的寿命

> 原文：<https://dev.to/buntine/aha-understanding-lifetimes-in-rust>

当我学习一些复杂的东西时，我经常发现自己在等待[尤里卡效应](https://en.wikipedia.org/wiki/Eureka_effect)(也称为“啊哈！瞬间”)。维基百科将尤里卡效应定义为“突然理解一个以前无法理解的问题或概念的普通人类体验”。这一效应已经被理解了几千年，最著名的是被伟大的希腊数学家阿基米德推广，他发现了如何坐在浴缸里测量物体的体积。

在这篇文章中，我想分享我自己的“啊哈！”与生锈的[寿命](https://doc.rust-lang.org/book/lifetimes.html)搏斗的时刻。我希望这能帮助你们中的一些人登上铁锈山的顶峰。

我将假设读者已经知道一些基本的 Rust，并且已经阅读了关于[所有权](https://doc.rust-lang.org/book/ownership.html)、[寿命](https://doc.rust-lang.org/book/lifetimes.html)和[借贷](https://doc.rust-lang.org/book/references-and-borrowing.html)的文档。

## 一个简单的场景

首先，我想提出一个简单的，适当设计的片段，要求我们规定一个生命周期

```
// Our Person struct consists of a name and an optional reference to another Person.
#[derive(Debug, Eq, PartialEq)]
struct Person<'a> {
    name: &'static str,
    parent: Option<&'a Person<'a>>,
}

impl<'a> Person<'a> {
    fn new(name: &'static str, parent: Option<&'a Person<'a>>) -> Person<'a> {
        Person{name: name, parent: parent}
    }

    // Rust lets us elide the lifetime of &self here.
    fn parents_name(&self) -> Option<&'static str> {
        self.parent.and_then(|p| Some(p.name))    
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

以及实现它的一些代码:

```
let jane = Person::new("Jane", None);
let tom = Person::new("Tom", Some(&jane));

assert_eq!(tom.parent.unwrap().name, "Jane");
assert_eq!(tom.parents_name(), Some("Jane"));
assert_eq!(jane.parent, None);
assert_eq!(jane.parents_name(), None); 
```

Enter fullscreen mode Exit fullscreen mode

## 只是个合同！

如果我们省略了前面例子中的生存期说明符，我们就会遇到问题:

```
$ cargo test Compiling lifetimes v0.1.0 (file:///home/andrew/dev/rust/lifetimes)
  src/lib.rs:4:20: 4:27 error: missing lifetime specifier [E0106]
  src/lib.rs:4     parent: Option<&Person>, 
```

Enter fullscreen mode Exit fullscreen mode

这是为什么呢？嗯，这是因为 Rust 需要一种方法来确保任何被借用的人都不会比任何其他借用它的人活得长。这是我第一次“啊哈！”。当我们显式地指定生存期时，**我们只是与编译器签订了一个合同，保证给定的资源在某个范围内可用**。从某种意义上说，我们是在向编译器承诺。但是我们都知道承诺可以被打破，所以 Rust 仍然为我们做了所有必要的检查。

我们是*而不是*“创造”一生。我们也没有告诉 Rust 允许引用存在于它不应该存在的地方。我们只是告诉编译器，如果调用代码违反了我们规定的生命周期规则，就要抱怨。

## 名字里有什么？

那么到底哪个作用域是**还是**？是指我们这种人的类型吗？不，**a**由呼叫代码定义。我们的类型只是决定了我们借入的值必须存在多长时间。把它想成“任何一生”a，而不是“称为 a 的特定一生”。构造函数很好地演示了这一点。看函数签名:

```
fn new(name: &'static str, parent: Option<&'a Person<'a>>) -> Person<'a> 
```

Enter fullscreen mode Exit fullscreen mode

这里我们说**“new 是一个函数，它接受对一个人的引用，这个人必须活一个我们称之为‘a’的一生，它返回一个新的人，这个人包含一个必须活同样一生的引用”**。

看到了吗？我们已经在语法层次上说明了被借用的值(父值)将与借用它的对象(返回值)具有相同的生存期。事实上，借来的价值可能比它的容器更长寿。我们的生存期注释只是指该值必须存在的最小生存期。

## 但是为什么呢？

为什么 Rust 不能在引擎盖下为我们解决这个问题呢？为什么 Rust 不能确保一个对象按照程序的要求持续一段时间呢？例如，在 [Go](https://golang.org/) 中超出其作用域的对象将被堆分配，并在稍后由垃圾收集器处理。嗯，问题就在这里:**这样的便利需要一个垃圾收集器**，这会给我们的程序带来性能上的打击。

**编辑:**正如 Reddit 用户 andytoshi 在[的评论](https://www.reddit.com/r/rust/comments/3z4fna/ive_documented_some_of_my_aha_moments_when/cyjelri)中所说，Rust 的所有权和借用系统不仅仅是“出于性能原因避免垃圾收集”。潜在的更重要的是，它们还保证了你对*的任何借用值都不会从你的下面发生变化*，即使你对某个东西有一个可变的引用。这给了我们的代码更高程度的合理性，并降低了整体的复杂性。

Rust 的所有权和生命周期系统允许编译器在编译时间*验证我们程序的内存安全性。我们不必像在 Ruby、Python、Go 等语言中那样在运行时将此任务委托给垃圾收集器，也不必像在 c 语言中那样自己手动分配/释放内存，事实上，在这一点上可能值得一提的是，所有这些关于生命周期的晦涩难懂的东西只在编译时才被关注。我们只是指我们的借用必须存在的词法范围。*

Rusts 内存模型是 Rust 称之为“零成本抽象”的少数事物之一，这让我想到了下一个啊哈！

## 我们的项目零成本，而不是我们！

当我第一次开始阅读这些零成本抽象概念时，我想知道它在这里是如何应用的，因为所涉及的学习曲线看起来肯定不像是“零成本”的。但后来我意识到，当 Rustaceans 人谈论零成本时，他们只是在谈论程序的运行时性能。啊哈！事实上，在生存期的情况下，Rust 实际上通过将额外的规则推给程序员，完全消除了垃圾收集器的必要性。如果你的程序可以编译——它几乎肯定是内存安全的。这些规则迫使我们提前考虑我们编程方式的内存安全，而不是在几个月后的错误修复中。是的，这可能会增加工作量，但无疑会让我们在未来省去很多麻烦！

## 最后一点

通常当我处理简单的结构时，我发现自己想知道为什么 Rust 不能像在某些函数声明中那样省略。例如，当我们说:

```
struct Person {
    name: &str,
    parent: Option<&Person>,
} 
```

Enter fullscreen mode Exit fullscreen mode

不会生锈只是把它翻译成引擎盖下的这个:

```
struct Person<'a> {
    name: &'a str,
    parent: Option<&'a Person<'a>>,
} 
```

Enter fullscreen mode Exit fullscreen mode

这一点在 Rust 社区引起了一些讨论。一些核心贡献者甚至认为现代 Rust 应该在这个场景中消失。

这里需要记住的重要一点是，Rust 通常更喜欢清晰而不是简洁。我认为耶胡达·卡茨在上面的回复中举了一个很好的例子。如果我们能够省略生存期注释，那么实现代码可能会像这样:

```
let people: Vec<Person> = â€¦; 
```

Enter fullscreen mode Exit fullscreen mode

我们突然失去了一个事实，那就是人包含着一种借来的价值。在复杂的现实软件中，这可能会导致令人困惑的错误。不过，我想在 Rust 的未来版本中，这方面会有一些变化。

## 结束

目前我就知道这些。我希望这里面的东西对你的旅程有所帮助。如果你注意到我说了一些不正确的话，请随时纠正我。提前感谢！