# 位标志 1.0.1 -第 1 部分

> 原文：<https://dev.to/graysonarts/bitflags-1-0-1-part-1-5kb>

这是我不久前在博客上写的一篇文章，我以为我已经把它发给了 dev.to，但显然，我没有。Bitflags 目前的版本是 1.2.1，所以这与当前的 RustLang 有点过时

这是我试图通过查看真实的工作实例并解释它们的作用来更好地理解 Rust。我远不是一个称职的 rust 程序员，所以如果我做错了什么，请留下评论和更正，我会根据你的反馈更新帖子，并给予适当的编辑。

系列中的第一个将是 [`bitflags`](https://github.com/rust-lang-nursery/bitflags/tree/1.0.1) 模块。我链接到了`1.0.1`版本，因为这是我开始写这篇文章时的最新版本。我将链接到重要的线，但也将包括一些线直接从板条箱来谈论它。

## 板条箱是做什么的？

`bitflags`箱创建 C 风格的位掩码字段。你知道，那些你`OR`值在一起配置的“东西”。你可以在旧的 C APIs 和操作系统级 API 中看到很多。这个 crate 允许您以类型安全的方式定义它们，并将它们传递给 C APIs。

## 录入宏

机箱的入口点是`bitflags!`宏。这就是奇迹发生的地方。

```
#[macro_export]
macro_rules! bitflags { 
```

Enter fullscreen mode Exit fullscreen mode

我们从宏的宏规则开始。这里没什么特别的，这是在 Rust 中定义宏的标准方式，但是 Rust 中的宏有点奇怪，所以接下来的部分需要我深入理解。稍微看了一下关于宏的 Rust 文档，给了我一点时间来研究 T2。

```
 (
        $(#[$outer:meta])*
        pub struct $BitFlags:ident: $T:ty {    
            $(
                $(#[$inner:ident $($args:tt)*])*
                const $Flag:ident = $value:expr;
            )+
        } 
```

Enter fullscreen mode Exit fullscreen mode

这第一部分是用来定义宏内部的内容，但是如果你不知道你在看什么，它就有点迟钝了，所以让我们把它进一步分解。

```
$(#[$outer:meta])* 
```

Enter fullscreen mode Exit fullscreen mode

如果您记得如何使用`bitflags!`宏，它包含一个`struct`。这个位意味着您可以将外部属性(以`#[...]`的形式)附加到结构上。在这种情况下，我们将它们命名为`$outer`，以便稍后在生成的代码中使用。

```
pub struct $BitFlags:ident: $T:ty { 
```

Enter fullscreen mode Exit fullscreen mode

宏的下一个功能是在调用体内部定义一个结构。`$BitFlags:ident`创建了一个变量，它保存了一个标识符，在本例中，就是你调用宏时给结构起的名字。`$T:ty`部分表示您还需要为将要生成的位标志指定一种类型。即使这没有在宏中编码，它也应该是某种无符号整数类型。

上面代码块的其余部分与第一部分类似，它定义了在结构中指定类似于`const A = 0b00000001;`的行的能力。

```
 ) => {
        __bitflags! { 
```

Enter fullscreen mode Exit fullscreen mode

模式匹配的结束是这个宏将要生成的内容的开始。该代码调用了另一个宏，我们将在查看实现时讨论这个宏。

```
 $(#[$outer])*
            (pub) $BitFlags: $T {
                $(
                    $(#[$inner $($args)*])*
                    $Flag = $value;
                )+
            }
        }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

这个宏的其余部分是将被传递到实现宏中的主体。它将属性和任何`const`声明传递到 body 中，并且显然从它们中剥离了 const。

第一批宏的其余部分大致相同，主要区别仅在于结构上的可见性和其他修饰符。我不会重复这些内容，所以让我们继续讨论实现细节。

## 内部运转情况

```
#[macro_export]
#[doc(hidden)]
macro_rules! __bitflags {
    (
        $(#[$outer:meta])*
        ($($vis:tt)*) $BitFlags:ident: $T:ty {
            $(
                $(#[$inner:ident $($args:tt)*])*
                $Flag:ident = $value:expr;
            )+
        } 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们为宏定义了另一种模式。这段代码与前面的模式非常相似，除了我们现在在模式中包含了 visiblility。我认为他们选择在公共接口中重复定义可见性的宏模式的主要原因是为了明确支持哪些类型的可见性。

```
 ) => {
        #[derive(Copy, PartialEq, Eq, Clone, PartialOrd, Ord, Hash)]
        $(#[$outer])*
        $($vis)* struct $BitFlags {
            bits: $T,
        } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们开始生成一些真正的 rust 代码。这定义了一个名为`$BitFlags`的结构，该结构只有一个名为`bits`的成员。它还派生出一系列有用特征的实现，这些特征将使位标志的使用更加容易。

```
 __impl_bitflags! {
            $BitFlags: $T {
                $(
                    $(#[$inner $($args)*])*
                    $Flag = $value;
                )+
            }
        }
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有另一个间接到另一个实现特定的宏。`__impl_bitflags`是一个相当大的宏，所以我将它分成一篇单独的文章。第 2 部分即将推出

如果你喜欢这篇文章，或者如果你有任何反馈，更正或任何东西，请在下面留下评论。