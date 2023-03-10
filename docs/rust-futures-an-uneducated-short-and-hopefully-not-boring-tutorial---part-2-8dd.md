# 锈期货:一个未受教育的，短，希望不无聊的教程-第 2 部分

> 原文：<https://dev.to/mindflavor/rust-futures-an-uneducated-short-and-hopefully-not-boring-tutorial---part-2-8dd>

## [T1】简介](#intro)

在本系列的第一篇文章中，我们看到了如何在 Rust 中使用期货。如果操作得当，它们功能强大且非常容易使用。在这第二篇文章中，我们将关注常见的陷阱，并希望能够避免它们。

## 错误故障

连锁期货很容易。我们看到了如何利用`and_then()`函数来做到这一点。但是在前面的部分中，我们使用了`Box<Error>`特征作为错误类型。为什么我没有使用更具体的错误类型？原来当你链接期货时，你必须返回*总是相同的错误类型*。

> 链接期货时，错误类型必须相同。

让我们试着演示一下。

我们有两种类型，叫做`ErrorA`和`ErrorB`(是的，我知道，不是特别原创)。我们将实现 [error::Error trait](https://doc.rust-lang.org/std/error/trait.Error.html) ，尽管严格来说这并不是必需的(但这是一个好的实践，我觉得)。`Error`特征也需要 [std::fmt::Display](https://doc.rust-lang.org/std/fmt/trait.Display.html) ，所以我们也要实现这个特征。

```
#[derive(Debug, Default)]
pub struct ErrorA {}

impl fmt::Display for ErrorA {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "ErrorA!")
    }
}

impl error::Error for ErrorA {
    fn description(&self) -> &str {
        "Description for ErrorA"
    }

    fn cause(&self) -> Option<&error::Error> {
        None
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`ErrorB`是一样的:

```
#[derive(Debug, Default)]
pub struct ErrorB {}

impl fmt::Display for ErrorB {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "ErrorB!")
    }
}

impl error::Error for ErrorB {
    fn description(&self) -> &str {
        "Description for ErrorB"
    }

    fn cause(&self) -> Option<&error::Error> {
        None
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了证明我的观点，我保持了简单的实现。现在让我们在未来使用这些结构。记住，未来只是一个语法略有不同的返回函数。

```
fn fut_error_a() -> impl Future<Item = (), Error = ErrorA> {
    err(ErrorA {})
}

fn fut_error_b() -> impl Future<Item = (), Error = ErrorB> {
    err(ErrorB {})
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在我们的`main`函数中调用它们:

```
let retval = reactor.run(fut_error_a()).unwrap_err();
println!("fut_error_a == {:?}", retval);

let retval = reactor.run(fut_error_b()).unwrap_err();
println!("fut_error_b == {:?}", retval); 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，结果是:

```
fut_error_a == ErrorA
fut_error_b == ErrorB 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利。现在让我们试着把这些期货串起来:

```
let future = fut_error_a().and_then(|_| fut_error_b()); 
```

Enter fullscreen mode Exit fullscreen mode

我们这里做的是调用`fut_error_a`函数，然后调用`fut_error_b` one。我们不关心由`fut_error_a`返回的值，所以我们用下划线将其丢弃。
用更复杂的术语来说，我们想把一个`impl Future<Item=(), Error=ErrorA>`和一个`impl Future<Item=(), Error=ErrorB>`连接起来。

我们试着编译一下:

```
Compiling tst_fut2 v0.1.0 (file:///home/MINDFLAVOR/mindflavor/src/rust/tst_future_2)
error[E0271]: type mismatch resolving `<impl futures::Future as futures::IntoFuture>::Error == errors::ErrorA`
   --> src/main.rs:166:32
    |
166 |     let future = fut_error_a().and_then(|_| fut_error_b());
    |                                ^^^^^^^^ expected struct `errors::ErrorB`, found struct `errors::ErrorA`
    |
    = note: expected type `errors::ErrorB`
               found type `errors::ErrorA` 
```

Enter fullscreen mode Exit fullscreen mode

错误是明显的。我们应该用一个`ErrorB`。我们提供了一个`ErrorA`来代替。总的来说:

> 链接期货时，第一个函数错误类型必须与链接的类型相同。

这正是 rustc 告诉我们的。链接的函数返回一个`ErrorB`，所以第一个函数也必须返回一个`ErrorB`。它没有这样做(而是返回一个`ErrorA`，所以编译失败。

我们如何处理这个问题？幸运的是，我们可以使用一个叫做`map_err`的*可链接的*方法。在我们的例子中，我们想要转换`ErrorB`中的`ErrorA`，所以我们在两个期货:
之间注入这个函数调用

```
let future = fut_error_a()
    .map_err(|e| {
        println!("mapping {:?} into ErrorB", e);
        ErrorB::default()
    })
    .and_then(|_| fut_error_b());

let retval = reactor.run(future).unwrap_err();
println!("error chain == {:?}", retval); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在编译并运行示例，输出将是:

```
mapping ErrorA into ErrorB
error chain == ErrorB 
```

Enter fullscreen mode Exit fullscreen mode

让我们进一步推这个例子。假设我们想要链接`ErrorA`，然后`ErrorB`，然后再链接`ErrorA`。大概是:

```
let future = fut_error_a()
    .and_then(|_| fut_error_b())
    .and_then(|_| fut_error_a()); 
```

Enter fullscreen mode Exit fullscreen mode

这条规则显然适用于成双成对的情况。它不能解释整个作品。所以我们必须在`fut_error_a`和`fut_error_b`之间映射`ErrorB`中的误差，然后反过来:在`fut_error_b`和`fut_error_a`之间映射`ErrorA`。丑陋但有效:

```
let future = fut_error_a()
    .map_err(|_| ErrorB::default())
    .and_then(|_| fut_error_b())
    .map_err(|_| ErrorA::default())
    .and_then(|_| fut_error_a()); 
```

Enter fullscreen mode Exit fullscreen mode

### “从”到救援

简化上述代码的一种方法是利用 [std::convert::From](https://doc.rust-lang.org/std/convert/trait.From.html) 特征。基本上，通过 From 特性，您可以告诉 Rust 如何自动地将一个结构转换成另一个结构。该特征消耗了原始错误，这在我们的实现中是没有问题的。此外，特征假设转换不会失败。最后，我们只能为我们自己的结构实现特征——也就是我们自己写的——所以这种方法不能总是被使用。

让我们实现`From<ErrorA> for ErrorB`和`FromInto<ErrorB> for ErrorA` :

```
impl From<ErrorB> for ErrorA {
    fn from(e: ErrorB) -> ErrorA {
        ErrorA::default()
    }
}

impl From<ErrorA> for ErrorB {
    fn from(e: ErrorA) -> ErrorB {
        ErrorB::default()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在只需调用`from_err()`函数而不是`map_err()`就可以简化之前的代码。Rust 会足够聪明地自己找出使用哪种转换:

```
let future = fut_error_a()
   .from_err()
   .and_then(|_| fut_error_b())
   .from_err()
   .and_then(|_| fut_error_a()); 
```

Enter fullscreen mode Exit fullscreen mode

代码仍然与错误转换混合在一起，但转换代码不再是内联的。这极大地提高了代码的可读性。
未来的机箱很聪明:`from_err`代码只有在出错的情况下才会被调用，所以使用它不会有运行时损失。

## 一生一世

Rust 签名特征是引用的显式生存期注释。然而，大多数时候，Rust 允许我们避免使用*寿命省略*来指定寿命。让我们看看它的实际效果。我们想编码一个函数，它接受一个字符串引用，如果成功，返回相同的字符串引用:

```
fn my_fn_ref<'a>(s: &'a str) -> Result<&'a str, Box<Error>> {
    Ok(s)
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`<'a>`部分。我们在宣告一生。然后用`s: &'a str`我们创建一个参数，它将接受字符串引用，只要`'a`有效，这些字符串引用就必须有效。通过`Result<&' str, Box<Error>>`，我们告诉 Rust 我们的返回值将包含一个字符串引用。只要`'a`有效，该字符串引用就必须有效。换句话说，传递的字符串引用和返回的对象必须具有相同的生存期。

也就是说，这种语法非常冗长，Rust 允许我们在像这样的常见情况下避免指定生存期。所以我们可以这样重写函数:

```
fn my_fn_ref(s: &str) -> Result<&str, Box<Error>> {
    Ok(s)
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这里不再提到生命周期，尽管它们确实存在。这个函数声明简单得多，一看就懂。

但是...在处理期货时，你不能省略寿命。如果你试图把这个函数转换成和我们在之前的博文中所做的一样的`impl Future`函数:

```
fn my_fut_ref_implicit(s: &str) -> impl Future<Item = &str, Error = Box<Error>> {
    ok(s)
} 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一个错误。在我的例子中(使用`rustc 1.23.0-nightly (2be4cc040 2017-11-01)`)，我得到了一个编译器恐慌:

```
 Compiling tst_fut2 v0.1.0 (file:///home/MINDFLAVOR/mindflavor/src/rust/tst_future_2)
error: internal compiler error: /checkout/src/librustc_typeck/check/mod.rs:633: escaping regions in predicate Obligation(predicate=Binder(ProjectionPredicate(ProjectionTy { substs: Slice([_]), item_def_id: DefId { krate: CrateNum(15), index: DefIndex(0:330) => futures[59aa]::future[0]::Future[0]::Item[0] } }, &str)),depth=0)
  --> src/main.rs:39:36
   |
39 | fn my_fut_ref_implicit(s: &str) -> impl Future<Item = &str, Error = Box<Error>> {
   |                                    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

note: the compiler unexpectedly panicked. this is a bug.

note: we would appreciate a bug report: https://github.com/rust-lang/rust/blob/master/CONTRIBUTING.md#bug-reports

note: rustc 1.23.0-nightly (2be4cc040 2017-11-01) running on x86_64-unknown-linux-gnu

thread 'rustc' panicked at 'Box<Any>', /checkout/src/librustc_errors/lib.rs:450:8
note: Run with `RUST_BACKTRACE=1` for a backtrace. 
```

Enter fullscreen mode Exit fullscreen mode

不要为此烦恼，记住`impl Future`仍然只是每夜的特色。为了解决这个问题，我们只需**通过用生存期:
显式注释引用来避免**生存期省略

```
fn my_fut_ref<'a>(s: &'a str) -> impl Future<Item = &'a str, Error = Box<Error>> {
    ok(s)
} 
```

Enter fullscreen mode Exit fullscreen mode

这将按预期工作。

### 用寿命实现未来

显式表达生存期的需要超出了参数的范围。如果你返回一个受生存期约束的`impl Future`,你也必须注释它。例如，假设我们想从一个以`&str`为参数的函数中返回一个未来的字符串。我们知道我们必须用生存期来注释参数，所以我们可以试试这个:

```
fn my_fut_ref_chained<'a>(s: &'a str) -> impl Future<Item = String, Error = Box<Error>> {
    my_fut_ref(s).and_then(|s| ok(format!("received == {}", s)))
} 
```

Enter fullscreen mode Exit fullscreen mode

这是行不通的:返回的未来不受`'a`约束。错误是:

```
error[E0564]: only named lifetimes are allowed in `impl Trait`, but `` was found in the type `futures::AndThen<impl futures::Future, futures::FutureResult<std::string::String, std::boxed::Box<std::error::Error + 'static>>, [closure@src/main.rs:44:28: 44:64]>`
  --> src/main.rs:43:42
   |
43 | fn my_fut_ref_chained<'a>(s: &'a str) -> impl Future<Item = String, Error = Box<Error>> {
   |                                          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
```

Enter fullscreen mode Exit fullscreen mode

要解决这个错误，你所要做的就是将`'a`添加到`impl Future`声明中，就像这样:

```
fn my_fut_ref_chained<'a>(s: &'a str) -> impl Future<Item = String, Error = Box<Error>> + 'a {
    my_fut_ref(s).and_then(|s| ok(format!("received == {}", s)))
} 
```

Enter fullscreen mode Exit fullscreen mode

这要归功于 HadrienG。参见[在连锁期货中命名生命周期的麻烦](https://users.rust-lang.org/t/solved-trouble-with-named-lifetimes-in-chained-futures/11943)。

现在你可以像往常一样使用你的反应器调用这个函数:

```
let retval = reactor
    .run(my_fut_ref_chained("str with lifetime"))
    .unwrap();
println!("my_fut_ref_chained == {}", retval); 
```

Enter fullscreen mode Exit fullscreen mode

并得到预期的输出:

```
my_fut_ref_chained == received == str with lifetime 
```

Enter fullscreen mode Exit fullscreen mode

## 结束语

在下一篇文章中，我们将报道反应堆。我们还将从头开始编写未来的实现结构。

* * *

快乐编码