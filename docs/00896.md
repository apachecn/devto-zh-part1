# 2018 年可反驳的 Let 和 Rust

> 原文：<https://dev.to/cad97/refutable-let-and-rust-in-2018-4l3k>

这检验了被推迟的 [RFC #1303“增加一个`let...else`表达式”](https://github.com/rust-lang/rfcs/pull/1303)，解决了 RFC 问题 [#373“明确可反驳的`let`](https://github.com/rust-lang/rfcs/issues/373) 。

这可能会像 RFC 一样被格式化，我很乐意让它适应新的 RFC，因为旧的 RFC 已经被推迟了将近两年。

## 一个激励人心的例子

考虑一个简单的例子:

```
if let Some(a) = make_a() {
    if let Some(b) = make_b(a) {
        if let Some(c) = make_c(b) {
            Ok(c)
        } else {
            Err("Failed to make C")?
        }
    } else {
        Err("Failed to make B")?
    }
} else {
    Err("Failed to make A")?
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有两个关键问题。第一个是错误处理的局部性。这个小例子的第 12 行返回了第 1 行的错误。第二种是向右漂移。函数的实际内容(这里只有`Ok(c)`)缩进了三级。为了解决这两个问题，RFC 引入了“可反驳的字母绑定”。

首先，让我解决这个最小示例的明显重构问题。当前的函数签名如下:

```
fn make_a()     -> Option<A>;
fn make_b(a: A) -> Option<B>;
fn make_c(b: B) -> Option<C>; 
```

Enter fullscreen mode Exit fullscreen mode

显而易见的“最佳”答案是让这些返回一个带有描述性错误消息的`Result`，然后您可以用`?`来传播它。然而，在真实情况下，也许`Option`确实是返回的正确语义类型，也许它是您不能更改的供应商代码，等等。等等..

其次:你可以。这是我今天要写的。老实说，我可能还是会这样写，因为`ok_or_else`正是我想要的行为，而且非常清楚。但是为了便于讨论，假设这是一个更复杂的例子，比如析构一个复杂的 ADT 枚举。为了简单起见，我在这里使用`Option`。

第三个选项是使用`match` :
进行析构

```
let a = match make_a() {
    Some(a) => a,
    _ => Err("Failed to make A")?,
};
let b = match make_b(a) {
    Some(b) => b,
    _ => Err("Failed to make B")?,
};
let c = match make_c(b) {
    Some(c) => c,
    _ => Err("Failed to make C")?,
};
Ok(c) 
```

Enter fullscreen mode Exit fullscreen mode

或者通过“口吃”一个`if let` :

```
let a = if let Some(a) = make_a() {
    a
} else {
    Err("Failed to make A")?
};
let b = if let Some(b) = make_b(a) {
    b
} else {
    Err("Failed to make B")?
};
let c = if let Some(c) = make_c(b) {
    c
} else {
    Err("Failed to make C")?
};
Ok(c) 
```

Enter fullscreen mode Exit fullscreen mode

[游乐场](https://play.rust-lang.org/?gist=c0f017dc5a6d6e71b7c2feaae00c9ac0)

下面是使用可反驳 let 的同一个例子:

```
let Some(a) = make_a() else {
    Err("Failed to make A")?
};
let Some(b) = make_b(a) else {
    Err("Failed to make B")?
};
let Some(c) = make_c(b) else {
    Err("Failed to make C")?
};
Ok(c) 
```

Enter fullscreen mode Exit fullscreen mode

这实际上可以使用`macro_rules`宏来实现，尽管有些困难。[游乐场](https://play.rust-lang.org/?gist=1a50e5a509b310d3981db2e7b12fa453)

## 为什么？

可反驳 let 的要点是你要做一些析构，并且你想处理不能通过偏离函数来析构的情况。

`let...else`的简单去糖是以下变换:

```
let PAT = EXPR else BLOCK;
// =>
let (bindings) = match EXPR {
    PAT => (bindings),
    _ => BLOCK: !,
} 
```

Enter fullscreen mode Exit fullscreen mode

其中`BLOCK: !`是[类型归属](https://github.com/rust-lang/rust/issues/23416)。归因的`!`类型使得`BLOCK`需要发散(这实际上意味着`return`、`break`或`continue`)。这里是模式中所有指定绑定的元组，因此它们可以移出模式并移入包含范围。

您可以看到，这种模式减少了使用`match`或`let = if let`在本地处理这种提前返回模式的错误时所需的停顿。然而，更关键的是，这个*加强了分叉*。如果你写了一个`match`或者`let = if let`或者`unwrap_or_else`或者除了`?`之外的任何东西，那么错误处理分支可以改为默认值分支。在语言层次上要求分叉增加了读者在阅读代码时的保证。

## 问题和备选语法

考虑解析以下内容:

```
let foo = if bar { baz() } else { quux() };

// Option 1:
let foo = 
    (if bar { 
        baz()
    } else {
        quux()
    });

// Option 2:
let foo = (if bar { baz() })
else { quux() }; 
```

Enter fullscreen mode Exit fullscreen mode

如果`baz()`是类型`()`的话，第二个*是*实际上是一个有效的解析选项。`if COND { () }`在 expr 位置有效，类型为`()`。[游乐场](https://play.rust-lang.org/?gist=b7f691eec516d6f1bc893bebc184d3ca)

不幸的是，这种模糊性意味着`if PAT = EXPR else`不是一个可能的明确语法；`else`不在`expr`的后续集合中是有原因的。

其他提议的语法:

```
<keyword> let PAT = EXPR else BLOCK 
```

Enter fullscreen mode Exit fullscreen mode

遭受着同样的模糊。

```
if !let PAT = EXPR BLOCK 
```

Enter fullscreen mode Exit fullscreen mode

不含糊，但是受到重载`if let`的困扰，它没有将绑定放入包含它的范围。

```
<keyword> let PAT = EXPR BLOCK 
```

Enter fullscreen mode Exit fullscreen mode

明确，但需要一个新的关键字。关键词可能包括`unless`。

## 最近

最近出现的两个与此相关的 RFC 是 [#2221 保护子句流类型](https://github.com/rust-lang/rfcs/pull/2221)和 [#2260 if- and while- let 链](https://github.com/rust-lang/rfcs/pull/2260)。

前者希望从流类型中获得相同的功能，作者似乎不认为`let...else`是一个有效的“守卫”。我不会多说，以免我断言一些不真实的东西。

后者看起来允许链接`if let`来减少嵌套。虽然这看起来没有直接关系，但人们经常提到，拥有一个可反驳的 let 将大大减少在一个块级别将`if let`链接在一起的必要性。

## 期待#Rust2018

除非我错过了一些辉煌的显而易见的语法，否则对于可反驳的 let，`if !let PAT = EXPR BLOCK`似乎是唯一没有新关键字的可行解决方案。很可能，这将最终成为使用的语法，虽然现在可能是陌生的，但它可能会像现在的`if let`一样成为第二天性。

我可以介绍一个新的 RFC，建议再次使用这种语法，并详细说明其他语法的问题。也许那里的设计可以充分、恰当地🚲:shed:(你挑表情符号:轻微 _ 微笑:)。

对于 Rust2018 的剩余时间，我同意其他#Rust2018 帖子的大部分内容。铁锈可能最适合滴答循环，这一年用来偿还稳定债务。许多伟大的功能就在眼前，只需要在设计/实现工作的最后推动。

这并不是说不应该有新的事情发生；WebAssembly 工作应该继续，RFC 机器应该继续运转(尽管我们不应该告诉人们像 impl 周期截止前那样快速提交想法)。稳定关键工具，实现 impl 周期特性，以及(重新)阐明稳定性。以某种方式认可板条箱是高质量的、准备生产的图书馆。异步/等待/生成器。

这些都是已经开始的*伟大的*事情。让我们一起走向终结吧！

哦，还有我的永远不会发生的破坏清单:让所有的`Iterator` fn 返回`impl Iterator`而不是具体类型。`impl Trait`所有的东西。

更新到上面:u/QuietMisdreavus [用这个](https://www.reddit.com/r/rust/comments/7plf8z/examining_rfc_1303_and_rust2018/dsj5lnc/)指出了破绽:

> 这其实在很多情况下是信息的丢失。几个`Iterator`包装器也根据它们包含的类型有条件地实现`DoubleEndedIterator`、`ExactSizeIterator`、`FusedIterator`等。据我所知，没有办法用`impl Trait`语法来表示。

意识到自己的错误后，这不再是我会做的选择。