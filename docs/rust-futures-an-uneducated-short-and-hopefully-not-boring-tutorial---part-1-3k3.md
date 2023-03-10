# 锈期货:一个未受教育，短，希望不无聊的教程-第 1 部分

> 原文：<https://dev.to/mindflavor/rust-futures-an-uneducated-short-and-hopefully-not-boring-tutorial---part-1-3k3>

# [T1】简介](#intro)

如果你是一名程序员并且你喜欢 Rust，你可能会意识到围绕 Rust 社区的*未来*运动。伟大的板条箱已经完全接受了它(例如[超级](https://github.com/hyperium/hyper))，所以我们必须能够使用它。但是如果你是像我一样的普通程序员，你可能很难理解它们。当然有官方的[克赖顿的教程](https://tokio.rs/docs/getting-started/futures/)，但是，虽然非常彻底，我发现很难付诸实践。

我猜我不是唯一的一个，所以我将分享我的发现，希望它们能帮助你更精通这个话题。

# 期货概括地说

期货是不会立即执行的特殊功能。它们将在未来执行(因此得名)。我们可能想使用未来函数而不是标准函数的原因有很多:性能、优雅、可组合性等等。缺点是它们很难编码。**非常**辛苦。当你不知道一个函数什么时候被执行，你怎么能理解因果关系呢？

由于这个原因，编程语言总是试图用有针对性的特性来帮助我们这些可怜的、迷失的程序员。

# 铁锈的未来

Rust 的未来实现，忠实于它的迷因，是一项正在进行的工作。所以，一如既往，你读的东西可能已经过时了。小心轻放。

Rust 的未来总是[结果](https://doc.rust-lang.org/std/result/):这意味着你必须指定预期回报类型和替代误差类型。

让我们选择一个函数，并将其转换为 future。我们的示例函数返回`u32`或装箱的[错误特征](https://doc.rust-lang.org/std/error/trait.Error.html)。这是:

```
fn my_fn() -> Result<u32, Box<Error>> {
    Ok(100)
} 
```

非常容易。不看相对的未来实现:

```
fn my_fut() -> impl Future<Item = u32, Error = Box<Error>> {
    ok(100)
} 
```

注意不同之处。首先返回类型不再是`Result`而是`impl` [未来](http://alexcrichton.com/futures-rs/futures/future/trait.Future.html)。这种语法(在撰写本文时仍然是夜间使用的)允许我们返回一个未来。`<Item = u32, Error = Box<Error>>`部分只是像以前一样详细描述返回类型和错误类型的一种更冗长的方式。

> 你需要`conservative_impl_trait`夜间功能来工作。你可以返回一个装箱的特征来代替，但是这样更麻烦。

还要注意`Ok(100)`返回值的情况。在结果函数中，我们使用大写的`Ok`枚举，在未来的函数中，我们使用小写的`ok`方法。

> 经验法则:在期货中使用小写回报方法。

总的来说没那么糟糕。但是怎么才能执行呢？可以直接调用标准函数。注意，因为我们返回的是一个`Result`，所以我们必须`unwrap()`来获取内部值。

```
let retval = my_fn().unwrap();
println!("{:?}", retval); 
```

当期货在实际执行之前返回(或者，更准确地说，我们正在返回将在未来执行的代码)时，我们需要一种方法*运行*它。为此我们使用了一个`Reactor`。只要创建它并调用它的`run`方法来执行一个 future。在我们的案例中:

```
let mut reactor = Core::new().unwrap();

let retval = reactor.run(my_fut()).unwrap();
println!("{:?}", retval); 
```

注意，这里我们展开的是`run(...)`返回值，而不是`my_fut()`返回值。

非常简单。

# 链接

期货最强大的功能之一是能够将期货链接在一起按顺序执行。考虑一下通过邮件邀请你的父母共进晚餐。你给他们发一封邮件，然后等待他们的回复。当你得到答案时，你就开始为他们准备晚餐(或者不准备，声称突发严重疾病)。连锁就是这样。让我们看一个例子。

首先，我们添加另一个函数，称为`squared`，作为标准函数和未来函数:

```
fn my_fn_squared(i: u32) -> Result<u32, Box<Error>> {
    Ok(i * i)
}

fn my_fut_squared(i: u32) -> impl Future<Item = u32, Error = Box<Error>> {
    ok(i * i)
} 
```

现在我们可以这样调用普通函数:

```
let retval = my_fn().unwrap();
println!("{:?}", retval);

let retval2 = my_fn_squared(retval).unwrap();
println!("{:?}", retval2); 
```

我们可以用多个反应器运行来模拟相同的流量，如下所示:

```
let mut reactor = Core::new().unwrap();

let retval = reactor.run(my_fut()).unwrap();
println!("{:?}", retval);

let retval2 = reactor.run(my_fut_squared(retval)).unwrap();
println!("{:?}", retval2); 
```

但是有更好的方法。一个未来，作为一种特质，有许多方法(我们将在这里涉及一些)。其中一个名为`and_then`，完全按照我们在上一段中编写的代码来做，但是没有显式的双反应器运行。让我们看看:

```
let chained_future = my_fut().and_then(|retval| my_fn_squared(retval));
let retval2 = reactor.run(chained_future).unwrap();
println!("{:?}", retval2); 
```

看第一行。我们创造了一个新的未来，由`my_fut`和`my_fut_squared`组成的`chained_future`。

棘手的部分是如何将结果从一个未来传递到下一个未来。Rust 通过闭包捕获(管道之间的东西，在我们的例子中是`|retval|`)来实现它。请这样想:

1.  安排`my_fut()`的执行。
2.  当`my_fut()`结束时，创建一个名为`retval`的变量，并将`my_fut()`执行的结果存储在其中。
3.  现在调度`my_fn_squared(i: u32)`的执行，将`retval`作为参数传递。
4.  将这个*指令表*打包成一个未来的`chained_future`。

下一行和前面类似:我们要求反应器运行`chained_future` *指令表*并给我们结果。

当然，我们可以链接无数的方法调用。也不要担心性能:你未来的链条将是**零成本**(除非你出于某种原因打拳)。

> 借检查可能会给你一个艰难的时间在捕获链。在这种情况下，尝试先用`move`移动捕获的变量。

## 混合期货和普通函数

你也可以用简单的函数来链接期货。这很有用，因为不是每个函数都需要是未来的。此外，您可能希望调用您无法控制的外部函数。

如果函数没有返回一个`Result`,您可以简单地在闭包中添加函数调用。例如，如果我们有这个简单的函数

```
fn fn_plain(i: u32) -> u32 {
    i - 50
} 
```

我们连锁的未来可能是:

```
let chained_future = my_fut().and_then(|retval| {
    let retval2 = fn_plain(retval);
    my_fut_squared(retval2)
});
let retval3 = reactor.run(chained_future).unwrap();
println!("{:?}", retval3); 
```

如果你的普通函数返回一个`Result`有一个更好的方法。让我们链接我们的`my_fn_squared(i: u32) -> Result<u32, Box<Error>>`函数。

你不能叫`and_then`来叫`Result`，但是期货创造了你所拥有的！有一种叫做 [done](https://docs.rs/futures/0.1.3/futures/fn.done.html) 的方法可以将一个`Result`转换成一个`impl Future`。这对我们意味着什么？我们可以用`done`方法包装我们的普通函数，然后我们可以把它当作一个未来来开始！

让我们来试试:

```
let chained_future = my_fut().and_then(|retval| {
    done(my_fn_squared(retval)).and_then(|retval2| my_fut_squared(retval2))
});
let retval3 = reactor.run(chained_future).unwrap();
println!("{:?}", retval3); 
```

注意第二行:`done(my_fn_squared(retval))`允许我们链接一个普通函数，就像是一个`impl Future`返回函数。现在尝试不使用`done`功能:

```
let chained_future = my_fut().and_then(|retval| {
    my_fn_squared(retval).and_then(|retval2| my_fut_squared(retval2))
});
let retval3 = reactor.run(chained_future).unwrap();
println!("{:?}", retval3); 
```

错误是:

```
 Compiling tst_fut2 v0.1.0 (file:///home/MINDFLAVOR/mindflavor/src/rust/tst_future_2)
error[E0308]: mismatched types
   --> src/main.rs:136:50
    |
136 |         my_fn_squared(retval).and_then(|retval2| my_fut_squared(retval2))
    |                                                  ^^^^^^^^^^^^^^^^^^^^^^^ expected enum `std::result::Result`, found anonymized type
    |
    = note: expected type `std::result::Result<_, std::boxed::Box<std::error::Error>>`
               found type `impl futures::Future`

error: aborting due to previous error

error: Could not compile `tst_fut2`. 
```

`expected type std::result::Result<_, std::boxed::Box<std::error::Error>> found type impl futures::Future`有些误导。事实上，我们应该传递一个`impl Future`,而我们使用了一个`Result`(而不是反过来)。这种困惑在期货交易中很常见(见[https://github.com/alexcrichton/futures-rs/issues/402](https://github.com/alexcrichton/futures-rs/issues/402))。我们将在帖子的第二部分讨论它。

# 仿制药

最后但同样重要的是，期货与泛型一起工作，没有任何魔法。

让我们看一个例子:

```
fn fut_generic_own<A>(a1: A, a2: A) -> impl Future<Item = A, Error = Box<Error>>
where
    A: std::cmp::PartialOrd,
{
    if a1 < a2 {
        ok(a1)
    } else {
        ok(a2)
    }
} 
```

这个函数返回传递的较小的参数。记住，为了实现未来的特征，我们需要付出和犯错，即使不期望有错误。此外，返回值(本例中的`ok`)是小写的(原因是`ok`是一个函数，而不是枚举)。

现在，我们可以一如既往地掌控未来:

```
let future = fut_generic_own("Sampdoria", "Juventus");
let retval = reactor.run(future).unwrap();
println!("fut_generic_own == {}", retval); 
```

我希望你现在已经掌握了要点:)。直到现在它应该是有意义的。你可能已经注意到我避免使用引用，只使用自有值。这是因为使用`impl Future`时，生存期的行为不同。我会在下一篇文章的[中解释如何使用地址。在下一篇](https://dev.to/mindflavor/rust-futures-an-uneducated-short-and-hopefully-not-boring-tutorial---part-2-8dd)[文章](https://dev.to/mindflavor/rust-futures-an-uneducated-short-and-hopefully-not-boring-tutorial---part-2-8dd)中，我们还将讨论链中的错误处理和`await!`宏。

# 备注

如果你想玩这些片段，你只需要添加到你的`Cargo.toml`文件中:

```
[dependencies]
futures="*"
tokio-core="*"
futures-await = { git = 'https://github.com/alexcrichton/futures-await' } 
```

并将这些行放在`src/main.rs`的顶部:

```
#![feature(conservative_impl_trait, proc_macro, generators)]

extern crate futures_await as futures;
extern crate tokio_core;

use futures::done;
use futures::prelude::*;
use futures::future::{err, ok};
use tokio_core::reactor::Core;
use std::error::Error; 
```

##### `futures-await`板条箱在这个帖子中并不真正需要，但我们将在下一个的[中使用它。](https://dev.to/mindflavor/rust-futures-an-uneducated-short-and-hopefully-not-boring-tutorial---part-2-8dd)

* * *

快乐编码